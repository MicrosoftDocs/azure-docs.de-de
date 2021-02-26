---
title: Informationen zum Mobilitätsdienst für die Notfallwiederherstellung von VMware-VMs und physischen Servern mit Azure Site Recovery | Microsoft-Dokumentation
description: Erfahren Sie mehr über den Mobilitätsdienst-Agent, um mit dem Azure Site Recovery-Dienst eine Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure auszuführen.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 8c90427e2dd4ecf23cb803c4f75dbee34c38f992
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898578"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informationen zum Mobilitätsdienst auf virtuellen VMware-Computern und physischen Servern

Wenn Sie die Notfallwiederherstellung für VMware-VMs und physische Server mithilfe von [Azure Site Recovery](site-recovery-overview.md) einrichten, installieren Sie den Site Recovery-Mobilitätsdienst auf jeder lokalen VMware-VM und auf jedem physischen Server. Der Mobilitätsdienst erfasst Datenschreibvorgänge auf dem Computer und leitet sie an den Site Recovery-Prozessserver weiter. Der Mobilitätsdienst wird durch die Mobilitätsdienst-Agent-Software installiert, die Sie mithilfe der folgenden Methoden bereitstellen können:

- [Pushinstallation](#push-installation): Wenn der Schutz im Azure-Portal aktiviert wird, installiert Site Recovery den Mobilitätsdienst auf dem Server.
- Manuelle Installation: Über die [Benutzeroberfläche](#install-the-mobility-service-using-ui) oder die [Eingabeaufforderung](#install-the-mobility-service-using-command-prompt) können Sie den Mobilitätsdienst manuell auf jedem Computer installieren.
- [Automatisierte Bereitstellung](vmware-azure-mobility-install-configuration-mgr.md): Sie können die Installation des Mobilitätsdiensts mithilfe von Softwarebereitstellungstools wie Configuration Manager automatisieren.

> [!NOTE]
> Der Mobilitätsdienst belegt ungefähr 6–10 % des Arbeitsspeichers auf den Quellcomputern für VMware-VMs oder physische Computer.

## <a name="antivirus-on-replicated-machines"></a>Virenschutz auf replizierten Computern

Wenn auf Computern, die Sie replizieren möchten, eine Virenschutzsoftware ausgeführt wird, schließen Sie den Installationsordner des Mobilitätsdiensts (_C:\ProgramData\ASR\agent_) von Virenschutzvorgängen aus. Dadurch wird sichergestellt, dass die Replikation wie erwartet funktioniert.

## <a name="push-installation"></a>Pushinstallation

Die Pushinstallation ist ein wesentlicher Bestandteil des Auftrags, der über das Azure-Portal zum [Aktivieren der Replikation](vmware-azure-enable-replication.md#enable-replication) ausgeführt wird. Nachdem Sie die Gruppe der zu schützenden VMs ausgewählt und die Replikation aktiviert haben, überträgt der Konfigurationsserver mittels Pushvorgang den Mobilitätsdienst-Agent auf die Server, installiert den Agent und schließt die Registrierung des Agents beim Konfigurationsserver ab.

### <a name="prerequisites"></a>Voraussetzungen

- Alle [Voraussetzungen](vmware-azure-install-mobility-service.md) für die Pushinstallation müssen erfüllt sein.
- Stellen Sie sicher, dass alle Serverkonfigurationen die Kriterien in der [Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure](vmware-physical-azure-support-matrix.md) erfüllen.
- Stellen Sie ab Version 9.36 für SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5 und Debian 7 sicher, dass das neueste Installationsprogramm [auf dem Konfigurationsserver und dem Prozessserver für die horizontale Skalierung verfügbar ist](#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

Der Workflow für die Pushinstallation wird in den folgenden Abschnitten beschrieben:

### <a name="mobility-service-agent-version-923-and-higher"></a>Mobilitätsdienst-Agent, Version 9.23 und höher

Weitere Informationen zu Version 9.23 finden Sie unter [Updaterollup 35 für Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Während einer Pushinstallation des Mobilitätsdiensts werden die folgenden Schritte ausgeführt:

1. Der Agent wird mithilfe von Push auf den Quellcomputer übertragen. Beim Kopieren des Agents auf den Quellcomputer kann es aufgrund verschiedener Umgebungsfehler zu Fehlern kommen. Schlagen Sie in [unserem Leitfaden](vmware-azure-troubleshoot-push-install.md) nach, wie Sie Fehler bei der Pushinstallation behandeln können.
1. Nachdem der Agent erfolgreich auf den Server kopiert wurde, wird auf dem Server eine Voraussetzungsprüfung ausgeführt.
   - Wenn alle Voraussetzungen erfüllt sind, wird die Installation gestartet.
   - Die Installation ist nicht erfolgreich, wenn mindestens eine der [Voraussetzungen](vmware-physical-azure-support-matrix.md) nicht erfüllt ist.
1. Im Rahmen der Agent-Installation wird der Volumeschattenkopie-Dienstanbieter (Volume Shadow Copy Service, VSS) für Azure Site Recovery installiert. Der VSS-Anbieter wird zum Generieren von anwendungskonsistenten Wiederherstellungspunkten verwendet. Wenn es bei der Installation des VSS-Anbieters zu einem Fehler kommt, wird dieser Schritt übersprungen, und die Agent-Installation wird fortgesetzt.
1. Wenn die Agent-Installation erfolgreich war, aber bei der Installation des VSS-Anbieters ein Fehler aufgetreten ist, wird der Status des Auftrags auf **Warnung** festgelegt. Dies wirkt sich nicht auf das Generieren absturzkonsistenter Wiederherstellungspunkte aus.

    - Informationen zum Generieren von anwendungskonsistenten Wiederherstellungspunkten finden Sie in [unserem Leitfaden](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) zum manuellen Abschließen der Installation des Site Recovery-VSS-Anbieters.
    - Wenn keine anwendungskonsistenten Wiederherstellungspunkte generiert werden sollen, [ändern Sie die Replikationsrichtlinie](vmware-azure-set-up-replication.md#create-a-policy), um anwendungskonsistente Wiederherstellungspunkte zu deaktivieren.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobilitätsdienst-Agent, Version 9.22 und niedriger

1. Der Agent wird mithilfe von Push auf den Quellcomputer übertragen. Beim Kopieren des Agents auf den Quellcomputer kann es aufgrund verschiedener Umgebungsfehler zu Fehlern kommen. Informieren Sie sich in [unserem Leitfaden](vmware-azure-troubleshoot-push-install.md), wie Sie Fehler bei der Pushinstallation behandeln können.
1. Nachdem der Agent erfolgreich auf den Server kopiert wurde, wird auf dem Server eine Voraussetzungsprüfung ausgeführt.
   - Wenn alle Voraussetzungen erfüllt sind, wird die Installation gestartet.
   - Die Installation ist nicht erfolgreich, wenn mindestens eine der [Voraussetzungen](vmware-physical-azure-support-matrix.md) nicht erfüllt ist.

1. Im Rahmen der Agent-Installation wird der Volumeschattenkopie-Dienstanbieter (Volume Shadow Copy Service, VSS) für Azure Site Recovery installiert. Der VSS-Anbieter wird zum Generieren von anwendungskonsistenten Wiederherstellungspunkten verwendet.
   - Wenn es bei der Installation des VSS-Anbieters zu Fehlern kommt, kann der Agent nicht installiert werden. Verwenden Sie zur Vermeidung von Fehlern bei der Agent-Installation [Version 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) oder höher, um absturzkonsistente Wiederherstellungspunkte zu generieren, und installieren Sie den VSS-Anbieter manuell.

## <a name="install-the-mobility-service-using-ui"></a>Installieren des Mobilitätsdiensts über die Benutzeroberfläche

### <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass alle Serverkonfigurationen die Kriterien in der [Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure](vmware-physical-azure-support-matrix.md) erfüllen.
- Suchen Sie basierend auf dem Betriebssystem des Servers nach dem [Installationsprogramm](#locate-installer-files).

>[!IMPORTANT]
> Verwenden Sie keine Installation über die Benutzeroberfläche, wenn Sie eine Azure IaaS-VM (Infrastructure-as-a-Service) von einer Azure-Region in eine andere replizieren. Verwenden Sie in diesem Fall die Installation über die [Eingabeaufforderung](#install-the-mobility-service-using-command-prompt).

1. Kopieren Sie die Installationsdatei auf den Computer, und führen Sie sie aus.
1. Wählen Sie unter **Installationsoption** den Eintrag **Mobilitätsdienst installieren** aus.
1. Wählen Sie den Installationsspeicherort aus, und klicken Sie auf **Installieren**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Optionsseite für die Installation des Mobilitätsdiensts":::

1. Überwachen Sie die Installation im **Installationsfortschritt**. Klicken Sie nach Abschluss der Installation auf **Mit der Konfiguration fortfahren**, um den Dienst beim Konfigurationsserver zu registrieren.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Screenshot: Fortschritt der Installation und aktive Schaltfläche „Mit der Konfiguration fortfahren“ nach Abschluss der Installation":::

1. Geben Sie unter **Konfigurationsserverdetails** die IP-Adresse und die konfigurierte Passphrase an.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Seite für die Registrierung des Mobilitätsdiensts":::

1. Klicken Sie auf **Registrieren**, um die Registrierung abzuschließen.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Letzte Seite die Registrierung des Mobilitätsdiensts":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Installieren des Mobilitätsdiensts über die Eingabeaufforderung

### <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass alle Serverkonfigurationen die Kriterien in der [Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure](vmware-physical-azure-support-matrix.md) erfüllen.
- Suchen Sie basierend auf dem Betriebssystem des Servers nach dem [Installationsprogramm](#locate-installer-files).

### <a name="windows-machine"></a>Windows-Computer

- Führen Sie an einer Eingabeaufforderung die folgenden Befehle aus, um das Installationsprogramm in einen lokalen Ordner (z. B. _C:\Temp_) auf dem Server zu kopieren, den Sie schützen möchten. Ersetzen Sie den Dateinamen des Installationsprogramms durch den tatsächlichen Dateinamen.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Führen Sie diesen Befehl aus, um den Agent zu installieren.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Führen Sie diese Befehle aus, um den Agent beim Konfigurationsserver zu registrieren.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Installationseinstellungen

Einstellung | Details
--- | ---
Syntax | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Setupprotokolle | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Erforderlicher Installationsparameter. Gibt an, ob der Mobilitätsdienst oder das Masterziel installiert werden soll.
`/InstallLocation`| Dieser Parameter ist optional. Gibt den Installationspfad des Mobilitätsdiensts an (beliebiger Ordner).
`/Platform` | Erforderlich. Gibt die Plattform an, auf der der Mobilitätsdienst installiert wird: <br/> **VMware** für VMware-VMs/physische Server. <br/> **Azure** für Azure-VMs.<br/><br/> Wenn Sie virtuelle Azure-Computer als physische Computer behandeln, geben Sie **VMware** an.
`/Silent`| Optional. Gibt an, ob das Installationsprogramm im unbeaufsichtigten Modus ausgeführt werden soll.

#### <a name="registration-settings"></a>Registrierungseinstellungen

Einstellung | Details
--- | ---
Syntax | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Agent-Konfigurationsprotokolle | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Erforderlicher Parameter. `<CSIP>` gibt die IP-Adresse des Konfigurationsservers an. Verwenden Sie eine beliebige gültige IP-Adresse.
`/PassphraseFilePath` |  Erforderlich. Speicherort der Passphrase. Verwenden Sie einen beliebiger UNC- oder lokalen Dateipfad.

### <a name="linux-machine"></a>Linux-Computer

1. Kopieren Sie aus einer Terminalsitzung das Installationsprogramm in einen lokalen Ordner (z. B. _/tmp_) auf dem Server, den Sie schützen möchten. Ersetzen Sie den Namen des Installationsprogramms durch den tatsächlichen Dateinamen Ihrer Linux-Distribution, und führen Sie dann die Befehle aus.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Führen Sie die Installation auf folgende Weise aus:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Nach Abschluss der Installation muss der Mobilitätsdienst beim Konfigurationsserver registriert werden. Führen Sie den folgenden Befehl aus, um den Mobilitätsdienst beim Konfigurationsserver zu registrieren.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Installationseinstellungen

Einstellung | Details
--- | ---
Syntax | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Erforderlicher Installationsparameter. Gibt an, ob der Mobilitätsdienst oder das Masterziel installiert werden soll.
`-d` | Dieser Parameter ist optional. Gibt den Installationsspeicherort des Mobilitätsdiensts an: `/usr/local/ASR`.
`-v` | Erforderlich. Gibt die Plattform an, auf der der Mobilitätsdienst installiert wird. <br/> **VMware** für VMware-VMs/physische Server. <br/> **Azure** für Azure-VMs.
`-q` | Optional. Gibt an, ob das Installationsprogramm im unbeaufsichtigten Modus ausgeführt werden soll.

#### <a name="registration-settings"></a>Registrierungseinstellungen

Einstellung | Details
--- | ---
Syntax | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Erforderlicher Parameter. `<CSIP>` gibt die IP-Adresse des Konfigurationsservers an. Verwenden Sie eine beliebige gültige IP-Adresse.
`-P` |  Erforderlich. Der vollständige Dateipfad der Datei, in der die Passphrase gespeichert ist. Verwenden Sie einen beliebigen gültigen Ordner.

## <a name="azure-virtual-machine-agent"></a>Azure-VM-Agent

- **Virtuelle Windows-Computer:** Ab Version 9.7.0.0 des Mobilitätsdiensts wird der [Azure VM-Agent](../virtual-machines/extensions/features-windows.md#azure-vm-agent) vom Mobilitätsdienst-Installer installiert. So wird sichergestellt, dass die Azure-VM die Agent-Installationsvoraussetzungen für die Verwendung beliebiger VM-Erweiterungen erfüllt, wenn ein Computer ein Failover zu Azure ausführt.
- **Virtuelle Linux-Computer:** Der [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) muss nach dem Failover manuell auf der Azure-VM installiert werden.

## <a name="locate-installer-files"></a>Suchen nach den Installerdateien

Wechseln Sie auf dem Konfigurationsserver zum Ordner _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Überprüfen Sie, welches Installationsprogramm Sie für Ihr Betriebssystem benötigen. Die folgende Tabelle enthält die Installationsprogramme für alle virtuellen VMware-Computer und alle Betriebssysteme von physischen Servern. Sie können sich vor dem Start die [unterstützten Betriebssysteme](vmware-physical-azure-support-matrix.md#replicated-machines) ansehen.

> [!NOTE]
> Der Dateiname verwendet die in der folgenden Tabelle gezeigte Syntax mit _version_ und _date_ als Platzhalter für die echten Werte. Die tatsächlichen Dateinamen ähneln denen in diesen Beispielen:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Installerdatei | Betriebssystem (nur 64-Bit)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
[Muss manuell heruntergeladen und in diesem Ordner abgelegt werden.](#rhel-5-or-centos-5-server) | Red Hat Enterprise Linux (RHEL) 5 </br> CentOS 5
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux 7 (RHEL) </br> CentOS 7:
`Microsoft-ASR_UA_version_RHEL8-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 8 </br> CentOS 8
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Umfasst SP2 und SP3.
[Muss manuell heruntergeladen und in diesem Ordner abgelegt werden.](#suse-11-sp3-server) | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_SLES15-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 15 
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_OL7-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 7 
`Microsoft-ASR_UA_version_OL8-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 8
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS-Server
`Microsoft-ASR_UA_version_UBUNTU-18.04-64_GA_date_release.tar.gz` | Ubuntu Linux 18.04 LTS-Server
`Microsoft-ASR_UA_version_UBUNTU-20.04-64_GA_date_release.tar.gz` | Ubuntu Linux 20.04 LTS-Server
[Muss manuell heruntergeladen und in diesem Ordner abgelegt werden.](#debian-7-server) | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8
`Microsoft-ASR_UA_version_DEBIAN9-64_GA_date_release.tar.gz` | Debian 9

## <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server"></a>Herunterladen des aktuellen Mobility Agent-Installationsprogramms für Server mit SUSE 11 SP3, RHEL 5 oder Debian 7

### <a name="suse-11-sp3-server"></a>SuSE 11 SP3-Server

Als **erforderliche Komponente zum Aktualisieren oder Schützen von SUSE Linux Enterprise Server 11 SP3-Computern** ab Version 9.36:

1. Stellen Sie sicher, dass der aktuelle Mobility Agent-Installer aus dem Microsoft Download Center heruntergeladen und auf dem Konfigurationsserver und allen Prozessservern für horizontales Hochskalieren im Pushinstallerrepository abgelegt wurde.
2. [Laden](site-recovery-whats-new.md) Sie das aktuelle Agent-Installationsprogramm für SUSE Linux Enterprise Server 11 SP3 herunter.
3. Navigieren Sie zum Konfigurationsserver, und kopieren Sie das Agent-Installationsprogramm für SUSE Linux Enterprise Server 11 SP3 an den Pfad „INSTALL_DIR\home\svsystems\pushinstallsvc\repository“.
1. Starten Sie nach dem Kopieren des aktuellen Installationsprogramms den Dienst „InMage PushInstall“ neu. 
1. Navigieren Sie nun zu zugeordneten Prozessservern für die horizontale Skalierung, und wiederholen Sie die Schritte 3 und 4.
1. **Wenn z. B.** der Installationspfad. „C:\Programme (x86)\Microsoft Azure Site Recovery“ lautet, lauten die oben genannten Verzeichnisse
    1. C:\Programme (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

### <a name="rhel-5-or-centos-5-server"></a>RHEL 5- oder CentOS 5-Server

**Voraussetzung für das Aktualisieren oder Schützen von Computern unter RHEL 5** ab Version 9.36:

1. Stellen Sie sicher, dass der aktuelle Mobility Agent-Installer aus dem Microsoft Download Center heruntergeladen und auf dem Konfigurationsserver und allen Prozessservern für horizontales Hochskalieren im Pushinstallerrepository abgelegt wurde.
2. [Laden](site-recovery-whats-new.md) Sie das aktuelle Agent-Installationsprogramm für RHEL 5 oder CentOS 5 herunter. 
3. Navigieren Sie zum Konfigurationsserver, und kopieren Sie das Agent-Installationsprogramm für RHEL 5 oder CentOS 5 an den Pfad „INSTALL_DIR\home\svsystems\pushinstallsvc\repository“.
1. Starten Sie nach dem Kopieren des aktuellen Installationsprogramms den Dienst „InMage PushInstall“ neu. 
1. Navigieren Sie nun zu zugeordneten Prozessservern für die horizontale Skalierung, und wiederholen Sie die Schritte 3 und 4.
1. **Wenn z. B.** der Installationspfad. „C:\Programme (x86)\Microsoft Azure Site Recovery“ lautet, lauten die oben genannten Verzeichnisse
    1. C:\Programme (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="debian-7-server"></a>Debian 7-Server

**Voraussetzung für das Aktualisieren oder Schützen von Computern unter Debian 7** ab Version 9.36:

1. Stellen Sie sicher, dass der aktuelle Mobility Agent-Installer aus dem Microsoft Download Center heruntergeladen und auf dem Konfigurationsserver und allen Prozessservern für horizontales Hochskalieren im Pushinstallerrepository abgelegt wurde.
2. [Laden](site-recovery-whats-new.md) Sie das aktuelle Agent-Installationsprogramm für Debian 7 herunter.
3. Navigieren Sie zum Konfigurationsserver, und kopieren Sie das Agent-Installationsprogramm für Debian 7 an den Pfad „INSTALL_DIR\home\svsystems\pushinstallsvc\repository“.
1. Starten Sie nach dem Kopieren des aktuellen Installationsprogramms den Dienst „InMage PushInstall“ neu. 
1. Navigieren Sie nun zu zugeordneten Prozessservern für die horizontale Skalierung, und wiederholen Sie die Schritte 3 und 4.
1. **Wenn z. B.** der Installationspfad. „C:\Programme (x86)\Microsoft Azure Site Recovery“ lautet, lauten die oben genannten Verzeichnisse
    1. C:\Programme (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="next-steps"></a>Nächste Schritte

[Einrichten der Pushinstallation für den Mobilitätsdienst](vmware-azure-install-mobility-service.md)
