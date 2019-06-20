---
title: Beheben von Fehlern bei der Mobility Service-Pushinstallation während der Aktivierung der Replikation für die Notfallwiederherstellung | Microsoft-Dokumentation
description: Beheben von Fehlern bei der Mobility Service-Installation während der Aktivierung der Replikation für die Notfallwiederherstellung
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 58c09c71aad2b6244f6e2f3d144c033665932f50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925575"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Beheben von Problemen bei Pushinstallationen von Mobility Service

Die Installation von Mobility Service ist ein wichtiger Schritt bei der Aktivierung der Replikation. Der Erfolg dieses Schritts hängt ausschließlich davon ab, ob Sie die Voraussetzungen erfüllen und mit unterstützten Konfigurationen arbeiten. Im Folgenden werden die Gründe für die häufigsten Fehler aufgeführt, die bei der Installation von Mobility Service auftreten:

* [Fehler bei Anmeldeinformationen/Berechtigungen](#credentials-check-errorid-95107--95108)
* [Anmeldefehler](#login-failures-errorid-95519-95520-95521-95522)
* [Verbindungsfehler](#connectivity-failure-errorid-95117--97118)
* [Fehler bei Datei- und Druckerfreigabe](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-Fehler](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nicht unterstützte Betriebssysteme](#unsupported-operating-systems)
* [Nicht unterstützte Startkonfigurationen](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Fehler bei der VSS-Installation](#vss-installation-failures)
* [Gerätename in GRUB-Konfiguration anstelle von Geräte-UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM-Volume](#lvm-support-from-920-version)
* [Neustartwarnungen](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Wenn Sie die Replikation aktivieren, versucht Azure Site Recovery, die Pushinstallation des Mobility Service-Agents auf Ihrem virtuellen Computer auszuführen. Im Rahmen dieses Vorgangs versucht der Konfigurationsserver, eine Verbindung mit dem virtuellen Computer herzustellen und den Agent zu kopieren. Folgen Sie der unten aufgeführten Schritt-für-Schritt-Anleitung zur Problembehandlung, um eine erfolgreiche Installation zu ermöglichen.

## <a name="credentials-check-errorid-95107--95108"></a>Überprüfung der Anmeldeinformationen (Fehler-ID: 95107 & 95108)

* Überprüfen Sie, ob das beim Aktivieren der Replikation ausgewählte Benutzerkonto **gültig und korrekt** ist.
* Azure Site Recovery benötigt ein **ROOT**-Konto oder ein Benutzerkonto mit **Administratorberechtigungen**, um die Pushinstallation ausführen zu können. Andernfalls wird die Pushinstallation auf dem Quellcomputer blockiert.
  * Überprüfen Sie unter Windows (**Fehler 95107**), ob das Benutzerkonto auf dem Quellcomputer über lokalen oder domänenspezifischen Administratorzugriff verfügt.
  * Wenn Sie kein Domänenkonto verwenden, müssen Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer deaktivieren.
    * Fügen Sie zum Deaktivieren der Remote-Benutzerzugriffssteuerung unter dem Registrierungsschlüssel „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System“ einen neuen DWORD-Eintrag hinzu: „LocalAccountTokenFilterPolicy“. Legen Sie den Wert auf 1 fest. Führen Sie zur Ausführung dieses Schritts den folgenden Befehl an der Eingabeaufforderung aus:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Unter Linux (**Fehler 95108**) müssen Sie das root-Konto auswählen, um die Installation des Mobility-Agents erfolgreich ausführen zu können. Außerdem müssen die SFTP-Dienste ausgeführt werden. Gehen Sie wie folgt vor, um das SFTP-Subsystem und die Kennwortauthentifizierung in der Datei „sshd_config“ zu aktivieren:
    1. Melden Sie sich als Root-Benutzer an.
    2. Wechseln Sie zur Datei „/etc/ssh/sshd_config“, und suchen Sie die Zeile, die mit „PasswordAuthentication“ beginnt.
    3. Heben Sie die Auskommentierung der Zeile auf, und ändern Sie den Wert in „yes“.
    4. Suchen Sie die Zeile, die mit „Subsystem“ beginnt, und heben Sie die Auskommentierung der Zeile auf.
    5. Starten Sie den SSHD-Dienst neu.

Wenn Sie die Anmeldeinformationen des ausgewählten Benutzerkontos ändern möchten, folgen Sie den [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) angegebenen Anweisungen.

## <a name="insufficient-privileges-failure-errorid-95517"></a>Fehler durch unzureichende Berechtigungen (Fehler-ID: 95517)

Wenn der zum Installieren des Mobilitäts-Agents ausgewählte Benutzer nicht über Administratorberechtigungen verfügt, wird dem Konfigurationsserver/Prozessserver für horizontales Hochskalieren das Kopieren der Mobility-Agent-Software auf den Quellcomputer untersagt. Dieser Fehler ist somit das Ergebnis eines Fehlers durch verweigerten Zugriff. Stellen Sie sicher, dass das Benutzerkonto über Administratorberechtigungen verfügt.

Wenn Sie die Anmeldeinformationen des ausgewählten Benutzerkontos ändern möchten, folgen Sie den [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) angegebenen Anweisungen.

## <a name="insufficient-privileges-failure-errorid-95518"></a>Fehler durch unzureichende Berechtigungen (Fehler-ID: 95518)

Wenn beim Versuch, sich beim Quellcomputer anzumelden, ein Fehler bei der Einrichtung der Vertrauensstellung zwischen der primären Domäne und der Arbeitsstation auftritt, tritt bei der Installation des Mobility-Agents ein Fehler mit der ID 95518 auf. Stellen Sie daher sicher, dass das Benutzerkonto zum Installieren des Mobility-Agents über Administratorberechtigungen für die Anmeldung über die primäre Domäne des Quellcomputers verfügt.

Wenn Sie die Anmeldeinformationen des ausgewählten Benutzerkontos ändern möchten, folgen Sie den [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) angegebenen Anweisungen.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Fehler bei der Anmeldung (Fehler-ID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Die Anmeldeinformationen des Benutzerkontos wurden deaktiviert (Fehler-ID: 95519)

Das beim Aktivieren der Replikation ausgewählte Benutzerkonto wurde deaktiviert. Um das Benutzerkonto zu aktivieren, lesen Sie [diesen](https://aka.ms/enable_login_user) Artikel, oder führen Sie den folgenden Befehl aus, wobei Sie *username* durch den tatsächlichen Benutzernamen ersetzen.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Die Anmeldeinformationen wurden aufgrund mehrerer fehlgeschlagener Anmeldeversuche gesperrt (Fehler-ID: 95520)

Durch mehrfache fehlerhafte Versuche des Zugriffs auf einen Computer wird das Benutzerkonto gesperrt. Der Fehler kann folgende Ursachen haben:

* Die bei der Konfiguration angegebene Anmeldeinformationen sind falsch, oder
* Das beim Aktivieren der Replikation ausgewählte Benutzerkonto ist falsch.

Ändern Sie daher die ausgewählten Anmeldeinformationen, indem Sie die [hier](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) angegebenen Anweisungen befolgen, und wiederholen Sie den Vorgang nach einiger Zeit.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Die Anmeldeserver sind auf dem Quellcomputer nicht verfügbar (Fehler-ID: 95521)

Dieser Fehler tritt auf, wenn die Anmeldeserver auf dem Quellcomputer nicht verfügbar sind. Die Nichtverfügbarkeit von Anmeldeservern führt zu einem Fehler bei der Anmeldeanforderung, sodass der Mobility-Agent nicht installiert werden kann. Um sich erfolgreich anzumelden, stellen Sie sicher, dass die Anmeldeserver auf dem Quellcomputer verfügbar sind, und starten Sie den Anmeldedienst. Ausführliche Anweisungen finden Sie [im Artikel](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) zu „Err Msg: There are Currently No Logon Servers Available“ (Fehlermeldung: Derzeit sind keine Anmeldeserver verfügbar.).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Der Anmeldedienst wird auf dem Quellcomputer nicht ausgeführt (Fehler-ID: 95522)

Der Anmeldedienst wird auf dem Quellcomputer nicht ausgeführt. Dies führte zu einem Fehler bei der Anmeldeanforderung. Der Mobility-Agent kann daher nicht installiert werden. Um dieses Problem zu beheben, stellen Sie sicher, dass für eine erfolgreiche Anmeldung der Anmeldedienst auf dem Quellcomputer ausgeführt wird. Um den Anmeldedienst zu starten, führen Sie den Befehl „net start Logon“ an einer Eingabeaufforderung aus, oder starten Sie den Dienst NetLogon im Task-Manager.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Konnektivitätsfehler (Fehler-ID: 95117 & 97118)**

Der Konfigurationsserver/Prozessserver für horizontales Hochskalieren versucht, eine Verbindung mit dem virtuellen Quellcomputer herzustellen, um den Mobility-Agent zu installieren. Dieser Fehler tritt auf, wenn der Quellcomputer aufgrund von Problemen mit der Netzwerkverbindung nicht erreichbar ist. Behebung:

* Stellen Sie sicher, dass Sie Ihren Quellcomputer über den Konfigurationsserver pingen können. Wenn Sie beim Aktivieren der Replikation einen Prozessserver für horizontales Skalieren ausgewählt haben, stellen Sie sicher, dass Sie Ihren Quellcomputer über den Prozessserver pingen können.
  * Verwenden Sie in der Befehlszeile des Quellservercomputers wie unten gezeigt Telnet zum Pingen des Konfigurationsservers oder Prozessservers für horizontales Skalieren mit dem HTTPS-Port (135), um festzustellen, ob Netzwerkkonnektivitätsprobleme bestehen oder der Port durch die Firewall blockiert wird.

     `telnet <CS/ scale-out PS IP address> <135>`
* Darüber hinaus gilt für **Linux-VMs** Folgendes:
  * Überprüfen Sie, ob die aktuellen Versionen der Pakete openssh, openssh-server und openssl installiert sind.
  * Stellen Sie unbedingt sicher, dass Secure Shell (SSH) auf Port 22 aktiviert ist und ausgeführt wird.
  * Die SFTP-Dienste müssen ausgeführt werden. Gehen Sie wie folgt vor, um das SFTP-Subsystem und die Kennwortauthentifizierung in der Datei „sshd_config“ zu aktivieren:
    * Melden Sie sich als Root-Benutzer an.
    * Wechseln Sie zur Datei „/etc/ssh/sshd_config“, und suchen Sie die Zeile, die mit „PasswordAuthentication“ beginnt.
    * Heben Sie die Auskommentierung der Zeile auf, und ändern Sie den Wert in „yes“.
    * Suchen Sie die Zeile, die mit „Subsystem“ beginnt, und heben Sie die Auskommentierung der Zeile auf.
    * Starten Sie den SSHD-Dienst neu.
* Ein Verbindungsversuch könnte fehlschlagen, wenn nach einer bestimmten Zeit keine ordnungsgemäße Antwort erfolgt ist, oder es ist ein Fehler bei einer hergestellten Verbindung aufgetreten, weil der verbundene Host nicht reagiert hat.
* Es kann sich um ein Verbindungs-, Netzwerk- oder Domänenproblem handeln. Es könnte sich auch um ein Problem beim Auflösen von DNS-Namen oder um ein Auslastungsproblem des TCP-Ports handeln. Überprüfen Sie, ob in Ihrer Domäne derartige bekannte Probleme vorkommen.

## <a name="connectivity-failure-errorid-95523"></a>Konnektivitätsfehler (Fehler-ID: 95523)

Dieser Fehler tritt auf, wenn das Netzwerk, in dem sich der Quellcomputer befindet, nicht gefunden wurde. Möglicherweise wurde er gelöscht oder ist nicht mehr verfügbar. Dieser Fehler kann nur behoben werden, indem Sie sicherstellen, dass das Netzwerk vorhanden ist.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Überprüfung der Datei- und Druckerfreigabedienste (Fehler-ID: 95105 & 95106)

Überprüfen Sie nach der Konnektivitätsprüfung, ob der Datei- und Druckerfreigabedienst auf Ihrem virtuellen Computer aktiviert ist. Diese Einstellungen sind erforderlich, um den Mobility-Agent auf den Quellcomputer zu kopieren.

**Windows 2008 R2 und vorherige Versionen**:

* Gehen Sie wie folgt vor, um die Datei- und Druckfreigabe über die Windows-Firewall zu aktivieren:
  * Öffnen Sie die Systemsteuerung, und klicken Sie auf „System und Sicherheit“ -> „Windows-Firewall“. Klicken Sie im linken Bereich auf „Erweiterte Einstellungen“, und klicken Sie dann in der Konsolenstruktur auf „Eingehende Regeln“.
  * Suchen Sie nach den Regeln „Datei- und Druckerfreigabe (NB-Sitzung eingehend)“ und „Datei- und Druckerfreigabe (SMB eingehend)“. Klicken Sie mit der rechten Maustaste auf jede Regel, und klicken Sie dann auf **Regel aktivieren**.
* Gehen Sie wie folgt vor, um die Dateifreigabe mit der Gruppenrichtlinie zu aktivieren:
  * Wechseln Sie zu „Start“, geben Sie „gpmc.msc“ ein, und starten Sie die Suche.
  * Öffnen Sie im Navigationsbereich die folgenden Ordner: „Richtlinie für ,Lokaler Computer‘“, „Benutzerkonfiguration“, „Administrative Vorlagen“, „Windows-Komponenten“ und „Netzwerkfreigabe“.
  * Doppelklicken Sie im Detailbereich auf **Verhindern, dass Benutzer Dateien in ihrem Profil freigeben**. Wenn Sie die Gruppenrichtlinieneinstellung deaktivieren und die Option zum Freigeben von Dateien für den Benutzer aktivieren möchten, klicken Sie auf „Deaktiviert“. Klicken Sie zum Speichern der Änderungen auf „OK“. Weitere Informationen finden Sie unter [Enable or disable File Sharing with Group Policy (Aktivieren oder Deaktivieren der Dateifreigabe mit der Gruppenrichtlinie)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Befolgen Sie für **höhere Versionen** die Anweisungen unter [Installieren des Mobilitätsdiensts für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern](vmware-azure-install-mobility-service.md), wenn Sie die Datei- und Druckerfreigabe aktivieren möchten.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Überprüfung der Konfiguration der Windows-Verwaltungsinstrumentation (Windows Management Instrumentation, WMI) (Fehlercode: 95103)

Aktivieren Sie nach der Überprüfung der Datei- und Druckerfreigabedienste den WMI-Dienst für private, öffentliche und Domänenprofile über die Firewall. Diese Einstellungen sind erforderlich, um die Remoteausführung auf dem Quellcomputer abzuschließen. Aktivieren:

* Wechseln Sie zur Systemsteuerung, klicken Sie auf „Sicherheit“, und klicken Sie dann auf „Windows-Firewall“.
* Klicken Sie auf „Einstellungen ändern“, und klicken Sie dann auf die Registerkarte „Ausnahmen“.
* Aktivieren Sie im Fenster „Ausnahmen“ das Kontrollkästchen für die Windows-Verwaltungsinstrumentation (WMI), um den WMI-Datenverkehr durch die Firewall zu aktivieren. 

Sie können den WMI-Datenverkehr durch die Firewall auch an der Eingabeaufforderung aktivieren. Verwenden Sie den folgenden Befehl: `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Weitere Artikel zur WMI-Problembehandlung finden Sie unter den folgenden Links.

* [Grundlegende WMI-Tests](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-Problembehandlung](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Problembehandlung bei WMI-Skripts und WMI-Diensten](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nicht unterstützte Betriebssysteme

Eine andere häufige Ursache für Fehler ist ein nicht unterstütztes Betriebssystem. Stellen Sie sicher, dass Sie ein unterstütztes Betriebssystem/eine unterstützte Kernel-Version verwenden, um eine erfolgreiche Installation von Mobility Service zu ermöglichen. Verwenden Sie keine privaten Patches.
In der [Dokumentation zur Unterstützungsmatrix](vmware-physical-azure-support-matrix.md#replicated-machines) können Sie sich eine Liste aller von Azure Site Recovery unterstützen Betriebssysteme und Kernelversionen ansehen.

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nicht unterstützte Startdatenträgerkonfigurationen (Fehler-ID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Start- und Systempartitionen/Volumes sind nicht derselbe Datenträger (Fehler-ID: 95309)

Vor Version 9.20 wurden Start- und Systempartitionen/Volumes auf verschiedenen Datenträgern als Konfiguration nicht unterstützt. Ab [Version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) wird diese Konfiguration unterstützt. Verwenden Sie für diese Unterstützung die neueste Version.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Der Startdatenträger ist nicht verfügbar (Fehler-ID: 95310)

Eine VM ohne Startdiskette kann nicht geschützt werden. Dadurch wird die reibungslose Wiederherstellung der VM während eines Failovervorgangs sichergestellt. Das Fehlen der Startdiskette führt zu einem Fehler beim Starten des Computers nach dem Failover. Stellen Sie sicher, dass die VM eine Startdiskette enthält, und versuchen Sie es dann noch einmal. Beachten Sie auch, dass die Verwendung mehrerer Startdisketten auf dem gleichen Computer nicht unterstützt wird.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Mehrere Startdatenträger auf dem Quellcomputer vorhanden (Fehler-ID: 95311)

Eine VM mit mehreren Startdisketten ist keine [unterstützte Konfiguration](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systempartition auf mehreren Datenträgern (Fehler-ID: 95313)

Vor Version 9.20 wurden Stammpartitionen oder Volumes auf mehreren Datenträgern als Konfiguration nicht unterstützt. Ab [Version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) wird diese Konfiguration unterstützt. Verwenden Sie für diese Unterstützung die neueste Version.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Fehler bei Schutz aktiviert, da in der GRUB-Konfiguration erwähnter Gerätename statt UUID vorliegt (Fehler-ID: 95320)

**Mögliche Ursache**: </br>
Die GRUB-Konfigurationsdateien („/boot/grub/menu.lst“, „/boot/grub/grub.cfg“, „/boot/grub2/grub.cfg“ oder „/etc/default/grub“) könnten den Wert für die Parameter **root** und **resume** als tatsächliche Gerätenamen statt UUID enthalten. Site Recovery bevorzugt den UUID-Ansatz, da Gerätenamen sich beim Neustart des virtuellen Computers ändern können, da die VM möglicherweise bei dem Failover nicht den Namen beibehält, was zu Problemen führen kann. Beispiel: </br>


- Die folgende Zeile stammt aus der GRUB-Datei **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- Die folgende Zeile stammt aus der GRUB-Datei **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Beachten Sie die fett formatierte Zeichenfolge oben: GRUB benutzt die tatsächlichen Gerätenamen für die Parameter „root“ und „resume“ statt UUID.
 
**Wie behebe ich das Problem?**<br>
Die Gerätenamen sollten mit dem entsprechenden UUID ersetzt werden.<br>


1. Suchen Sie die UUID des Geräts durch Ausführen des Befehls „blkid \<device name>“. Beispiel:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Ersetzen Sie den Gerätenamen jetzt die UUID im Format „root=UUID=\<UUID>“. Wenn Sie die Gerätenamen durch die UUID für die Parameter „root“ und „resume“ ersetzen, die oben in den Dateien „/boot/grub2/grub.cfg“, „/boot/grub2/grub.cfg“ oder „/etc/default/grub“ erwähnt werden, sehen die Zeilen in den Dateien folgendermaßen aus: <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Starten Sie den Schutz erneut.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installation des Mobilitätsdienst mit Neustartwarnung abgeschlossen (Fehler-ID: 95265, 95266)

Der Site Recovery-Mobilitätsdienst verfügt über viele Komponenten, einer davon ist der Filtertreiber. Der Filtertreiber wird nur bei einem Systemneustart in den Systemspeicher geladen. Das bedeutet, dass Korrekturen des Filtertreibers nur dann übernommen werden, wenn ein neuer Filtertreiber geladen wird, was nur zum Zeitpunkt des Systemneustarts erfolgen kann.

**Bitte beachten Sie**, dass es sich hierbei um eine Warnung handelt und die bestehende Replikation auch nach dem neuen Agent-Update funktioniert. Sie können jederzeit einen Neustart durchführen, um die Vorteile des neuen Filtertreibers zu nutzen. Wenn Sie jedoch keinen Neustart durchführen, funktioniert der alte Filtertreiber weiterhin. So werden nach einem Update ohne Neustart, abgesehen vom Filtertreiber, **die Vorteile anderer Verbesserungen und Korrekturen im Mobilitätsdienst übernommen**. Es wird zwar empfohlen, nach jedem Upgrade einen Neustart durchzuführen, aber es ist nicht erforderlich. Weitere Informationen dazu, wann ein Neustart erforderlich ist, finden Sie im Abschnitt [Neustart des Quellcomputers nach dem Upgrade des Mobilitäts-Agents](https://aka.ms/v2a_asr_reboot) in den Dienstupdates von Azure Site Recovery.

> [!TIP]
>Weitere Informationen zu bewährten Methoden für die Planung von Upgrades während des Wartungsfensters finden Sie unter [Unterstützung für die neuesten Betriebssystem-/Kernelversionen](https://aka.ms/v2a_asr_upgrade_practice) in den Dienstupdates von Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-Unterstützung ab Version 9.20

Vor Version 9.20 wurde LVM nur für Datenträger für Daten unterstützt. /boot muss sich auf einer Datenträgerpartition und nicht auf einem LVM-Volume befinden.

Ab [Version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) werden [Betriebssystemdatenträger auf LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) unterstützt. Verwenden Sie für diese Unterstützung die neueste Version.

## <a name="insufficient-space-errorid-95524"></a>Nicht genügend Speicherplatz (Fehler-ID: 95524)

Zum Kopieren des Mobility-Agents auf den Quellcomputer ist ein freier Speicherplatz von mindestens 100 MB erforderlich. Stellen Sie daher sicher, dass der Quellcomputer über den erforderlichen freien Speicherplatz verfügt, und wiederholen Sie den Vorgang.

## <a name="vss-installation-failures"></a>Fehler bei der VSS-Installation

Die VSS-Installation ist Teil der Installation des Mobility Service-Agents. Dieser Dienst wird beim Generieren von anwendungskonsistenten Wiederherstellungspunkten verwendet. Fehler bei der VSS-Installation können aus verschiedenen Gründen auftreten. Die genauen Fehler können Sie unter **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log** ermitteln. Einige häufige Fehler und die entsprechenden Lösungsschritte werden im folgenden Abschnitt beschrieben.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-Fehler -2147023170 [0x800706BE] – Exitcode 511

Dieses Problem tritt hauptsächlich auf, wenn eine Virenschutzsoftware die Vorgänge von Azure Site Recovery-Diensten blockiert. So lösen Sie dieses Problem:

1. Schließen Sie alle [hier](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) genannten Ordner aus.
2. Befolgen Sie die vom Anbieter der Antivirensoftware veröffentlichten Richtlinien, um die Blockierung der Registrierung der DLL in Windows aufzuheben.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-Fehler 7 [0x7] – Exitcode 511

Dies ist ein Laufzeitfehler, der aufgrund von unzureichendem Speicher für die VSS-Installation verursacht wird. Erhöhen Sie den Speicherplatz, um den Installationsvorgang erfolgreich abzuschließen.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-Fehler -2147023824 [0x80070430] – Exitcode 517

Dieser Fehler tritt auf, wenn der Azure Site Recovery-VSS-Anbieter-Dienst [zum Löschen markiert](https://msdn.microsoft.com/library/ms838153.aspx) ist. Versuchen Sie, VSS manuell auf dem Quellcomputer zu installieren, indem Sie die folgende Befehlszeile ausführen.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-Fehler -2147023841 [0x8007041F] – Exitcode 512

Dieser Fehler tritt auf, wenn die Datenbank des Azure Site Recovery-VSS-Anbieter-Diensts [gesperrt](https://msdn.microsoft.com/library/ms833798.aspx) ist. Versuchen Sie, VSS manuell auf dem Quellcomputer zu installieren, indem Sie die folgende Befehlszeile ausführen.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS Exitcode 806

Dieser Fehler tritt auf, wenn das für die Installation verwendete Benutzerkonto nicht über Berechtigungen zum Ausführen des CSScript-Befehls verfügt. Geben Sie die erforderlichen Berechtigungen für das Benutzerkonto an, um das Skript auszuführen und den Vorgang zu wiederholen.

### <a name="other-vss-errors"></a>Andere VSS-Fehler

Versuchen Sie, den VSS-Anbieter-Dienst manuell auf dem Quellcomputer zu installieren, indem Sie die folgende Befehlszeile ausführen.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS-Fehler: 0x8004E00F

Dieser Fehler tritt üblicherweise bei der Installation des Mobilitäts-Agents auf, wenn es zu Fehlern in DCOM kommt und DCOM sich in einem kritischen Zustand befindet.

Mithilfe des folgenden Verfahrens können Sie die Ursache des Fehlers ermitteln.

**Untersuchen der Installationsprotokolle**

1. Öffnen Sie das Installationsprotokoll unter C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Folgende Fehlermeldung weist auf dieses Problem hin:

    Das Aufheben der Registrierung des vorhandenen Anwendungstyps...  Create the catalogue object (Das Erstellen des Katalogobjekts...) Get the collection of Applications (Das Abrufen der Anwendungssammlung...) 

    FEHLER:

    - Fehlercode: -2147164145 [0x8004E00F]
    - Exitcode: 802

So lösen Sie das Problem:

Kontaktieren Sie das [Team für Microsoft Windows](https://aka.ms/Windows_Support), wenn Sie Hilfe beim Beheben des DCOM-Fehlers benötigen.

Wenn der DCOM-Fehler behoben ist, sollten Sie den VSS-Anbieter von Azure Site Recovery manuell über folgenden Befehl installieren:
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Wenn die Anwendungskonsistenz für Ihre Anforderungen für die Notfallwiederherstellung nicht entscheidend ist, können Sie die Installation des VSS-Anbieters umgehen. 

Gehen Sie folgendermaßen vor, um die Installation des VSS-Anbieters von Azure Site Recovery zu umgehen und diesen nach der Installation manuell zu installieren:

1. Installieren Sie den Mobilitätsdienst. 
   > [!Note]
   > 
   > Die Installation schlägt beim Schritt „Post install configuration“ (Konfiguration nach der Installation) fehl. 
2. So können Sie die Installation des VSS-Anbieters umgehen:
   1. Öffnen Sie das Installationsverzeichnis für Azure Site Recovery Mobility Service:
   
      C:\Programme (x86)\Microsoft Azure Site Recovery\agent
   2. Bearbeiten Sie die Installationsskripts **nMageVSSProvider_Install** und **InMageVSSProvider_Uninstall.cmd** für den VSS-Anbieter von Azure Site Recovery so, dass die Ausführung immer erfolgreich ist, indem Sie folgende Zeilen hinzufügen:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Führen Sie die Installation des Mobilitäts-Agents manuell erneut aus. 
4. Wenn die Installation erfolgreich ist und Sie bei der **Konfiguration** angelangt sind, sollten Sie die hinzugefügten Zeilen wieder entfernen.
5. Öffnen Sie eine Eingabeaufforderung als Administrator, und führen Sie den folgenden Befehl aus, um den VSS-Anbieter zu installieren:
   
    **C:\Program Files (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. Stellen Sie sicher, dass der VSS-Anbieter von Azure Site Recovery als Dienst in den Windows-Diensten installiert ist, und öffnen Sie die Komponentendienst-MMC, um sicherzustellen, dass der Azure Site Recovery-VSS-Anbieter aufgeführt wird.
10. Wenn die Installation des VSS-Anbieters weiterhin fehlschlägt, sollten Sie CX verwenden, um die Berechtigungsfehler in CAPI2 zu beheben.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Fehlgeschlagene Installation des VSS-Anbieters aufgrund des aktivierten Clusterdiensts auf einem Nicht-Clustercomputer

Hierdurch schlägt die Installation des Mobilitäts-Agents von Azure Site Recovery während der Installation des VSS-Anbieters für Azure Site Recovery (ASR) aufgrund eines COM+-Fehlers fehl, der die Installation des VSS-Anbieters verhindert.
 
### <a name="to-identify-the-issue"></a>Identifizieren des Problems

In dem Protokoll, das sich auf dem Konfigurationsserver unter „C:\ProgramData\ASRSetupLogs\UploadedLogs\<date-time>UA_InstallLogFile.log“ befindet, ist folgende Ausnahme enthalten:

COM+ konnte keine Daten mit dem Microsoft Distributed Transaction Coordinator austauschen. (Ausnahme von HRESULT: 0x8004E00F)

So lösen Sie das Problem:

1.  Wenn es sich bei diesem Computer um einen Nicht-Clustercomputer handelt, sollten Sie sicherstellen, dass keine Clusterkomponenten verwendet werden.
3.  Entfernen Sie die verwendeten Clusterkomponenten vom Computer.

## <a name="drivers-are-missing-on-the-source-server"></a>Fehlende Treiber auf dem Quellserver

Wenn die Installation des Mobilitäts-Agents fehlschlägt, sollten Sie die Protokolle unter C:\ProgramData\ASRSetupLogs darauf überprüfen, ob erforderliche Treiber in den Konfigurationen fehlen.
 
So lösen Sie das Problem:
  
1. Öffnen Sie die Registrierung mit einem Registrierungs-Editor wie „regedit.msc“.
2. Öffnen Sie den Knoten HKEY_LOCAL_MACHINE\SYSTEM.
3. Suchen Sie im Knoten System die Konfigurationen.
4. Öffnen Sie jede Konfiguration, und überprüfen Sie, ob folgende Windows-Treiber vorhanden sind:

   - Atapi
   - Vmbus
   - Storflt
   - Storvsc
   - intelide
 
Installieren Sie fehlende Treiber neu.

## <a name="next-steps"></a>Nächste Schritte

[Vorgehensweise](vmware-azure-tutorial.md) zum Einrichten der Notfallwiederherstellung für virtuelle VMware-Computer.
