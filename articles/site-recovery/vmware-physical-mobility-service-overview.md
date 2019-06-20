---
title: Informationen zum Mobilitätsdienst für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren mehr über den Mobilitätsdienst-Agent, um mit dem Azure Site Recovery-Dienst eine Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure auszuführen.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 929a4e4366c9e94ed4e1915406914991624f6baa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565553"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informationen zum Mobilitätsdienst auf virtuellen VMware-Computern und physischen Servern

Wenn Sie die Notfallwiederherstellung für VMware-VMs und physische Server mithilfe von [Azure Site Recovery](site-recovery-overview.md) einrichten, installieren Sie den Site Recovery-Mobilitätsdienst auf jedem lokalen virtuellen VMware-Computer und physischen Server.  Der Mobilitätsdienst erfasst Datenschreibvorgänge auf dem Computer und leitet sie an den Site Recovery-Prozessserver weiter. Sie können die den Mobilitätsdienst mithilfe der folgenden Methoden bereitstellen:

- [Pushinstallation](#push-installation): Site Recovery installiert den Mobilitäts-Agent auf dem Server, wenn der Schutz im Azure-Portal aktiviert wird.
- Manuelle Installation: Über die [Benutzeroberfläche](#install-mobility-agent-through-ui) oder die [Eingabeaufforderung](#install-mobility-agent-through-command-prompt) können Sie den Mobilitätsdienst manuell auf jedem Computer installieren.
- [Automatisierte Bereitstellung](vmware-azure-mobility-install-configuration-mgr.md): Sie können die Installation mithilfe von Softwarebereitstellungstools wie System Center Configuration Manager automatisieren.

## <a name="anti-virus-on-replicated-machines"></a>Virenschutz auf replizierten Computern

Wenn auf Computern, die Sie replizieren möchten, aktive Virenschutzsoftware ausgeführt wird, achten Sie darauf, den Installationsordner von Mobility Service von Virenschutzvorgängen auszuschließen (*C:\ProgramData\ASR\agent*). Dadurch wird sichergestellt, dass die Replikation wie erwartet funktioniert.

## <a name="push-installation"></a>Pushinstallation

Die Pushinstallation ist ein integraler Bestandteil des Auftrags [Replikation aktivieren](vmware-azure-enable-replication.md#enable-replication), die im Portal ausgelöst wird. Nachdem Sie die Gruppe der zu schützenden virtuellen Computer ausgewählt und „Replikation aktivieren“ ausgelöst haben, überträgt der Konfigurationsserver mittels Pushvorgang den Mobilitäts-Agent auf die Server, installiert den Agent und schließt die Registrierung des Agents beim Konfigurationsserver ab. Stellen Sie für den erfolgreichen Abschluss dieses Vorgangs Folgendes sicher:

- Alle [Voraussetzungen](vmware-azure-install-mobility-service.md) für die Pushinstallation müssen erfüllt sein.
- Alle Serverkonfigurationen müssen in die [Unterstützungsmatrix von VMware für ein Azure-Notfallwiederherstellungsszenario](vmware-physical-azure-support-matrix.md) fallen.

Die Einzelheiten des Workflows für die Pushinstallation werden in den folgenden Abschnitten beschrieben.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Ab [Version 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

Bei der Installation des Mobilitäts-Agents werden die folgenden Schritte ausgeführt

1. Der Agent wird per Pushvorgang auf den Quellcomputer übertragen. Beim Kopieren des Agents auf den Quellcomputer können aufgrund mehrerer Umgebungsfehler Fehler auftreten. Schlagen Sie in [unserem Leitfaden](vmware-azure-troubleshoot-push-install.md) nach, wie Sie Fehler bei der Pushinstallation behandeln können.
2. Nachdem der Agent erfolgreich auf den Server kopiert wurde, werden auf dem Server Voraussetzungsprüfungen ausgeführt. Die Installation schlägt fehl, wenn eine oder mehrere der [Voraussetzungen](vmware-physical-azure-support-matrix.md) nicht erfüllt werden. Wenn alle Voraussetzungen erfüllt werden, wird die Installation ausgelöst.
3. Im Rahmen der Installation des Mobilitäts-Agents wird auch der Azure Site Recovery-VSS-Anbieter auf dem Server installiert. Dieser Anbieter wird zum Generieren von anwendungskonsistenten Punkten verwendet. Wenn bei der Installation des VSS-Anbieters ein Fehler auftritt, wird dieser Schritt übersprungen, und die Agent-Installation wird fortgesetzt.
4. Wenn die Agent-Installation erfolgreich war, aber bei der Installation des VSS-Anbieters ein Fehler aufgetreten ist, wird der Auftragsstatus als „Warnung“ gekennzeichnet. Dies wirkt sich nicht auf die Generierung von absturzkonsistenten Punkten aus.

    a. Informationen zum Generieren von anwendungskonsistenten Punkten finden Sie in [unserem Leitfaden](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine), damit Sie die Installation des Site Recovery-VSS-Anbieters manuell abschließen können. </br>
    b.  Wenn keine anwendungskonsistenten Punkte generiert werden sollen, [ändern Sie die Replikationsrichtlinie](vmware-azure-set-up-replication.md#create-a-policy), um anwendungskonsistente Punkte zu deaktivieren.

### <a name="before-922-versions"></a>Vor Version 9.22

1. Der Agent wird per Pushvorgang auf den Quellcomputer übertragen. Beim Kopieren des Agents auf den Quellcomputer können aufgrund mehrerer Umgebungsfehler Fehler auftreten. Schlagen Sie in [unserem Leitfaden](vmware-azure-troubleshoot-push-install.md) nach, wie Sie Fehler bei der Pushinstallation behandeln können.
2. Nachdem der Agent erfolgreich auf den Server kopiert wurde, werden auf dem Server Voraussetzungsprüfungen ausgeführt. Die Installation schlägt fehl, wenn eine oder mehrere der [Voraussetzungen](vmware-physical-azure-support-matrix.md) nicht erfüllt werden. Wenn alle Voraussetzungen erfüllt werden, wird die Installation ausgelöst.
3. Im Rahmen der Installation des Mobilitäts-Agents wird auch der Azure Site Recovery-VSS-Anbieter auf dem Server installiert. Dieser Anbieter wird zum Generieren von anwendungskonsistenten Punkten verwendet. Wenn bei der Installation des VSS-Anbieters ein Fehler auftritt, schlägt die Agent-Installation fehl. Zur Vermeidung von Fehlern bei der Installation des Mobilitäts-Agents sollten Sie [Version 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) oder höher verwenden, um absturzkonsistente Punkte zu generieren und den VSS-Anbieter manuell zu installieren.

## <a name="install-mobility-agent-through-ui"></a>Installieren des Mobilitäts-Agents über die Benutzeroberfläche

### <a name="prerequisite"></a>Voraussetzung

- Alle Serverkonfigurationen müssen in die [Unterstützungsmatrix von VMware für ein Azure-Notfallwiederherstellungsszenario](vmware-physical-azure-support-matrix.md) fallen.
- [Suchen Sie das Installationsprogramm](#locate-installer-files) basierend auf dem Betriebssystem des Servers.

>[!IMPORTANT]
> Wenn Sie virtuelle Azure-IaaS-Computer von einer Azure-Region in eine andere replizieren, sollten Sie diese Methode nicht verwenden. Verwenden Sie stattdessen die befehlszeilenbasierte Installationsmethode.

1. Kopieren Sie die Installationsdatei auf den Computer, und führen Sie sie aus.
2. Wählen Sie unter **Installationsoption** den Eintrag **Mobilitätsdienst installieren** aus.
3. Wählen Sie den Installationspfad aus, und klicken Sie auf **Installieren**.

    ![Optionsseite für die Mobility Service-Installation](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Überwachen Sie die Installation im **Installationsfortschritt**. Klicken Sie nach Abschluss der Installation auf **Mit der Konfiguration fortfahren**, um den Dienst beim Konfigurationsserver zu registrieren.

    ![Seite für die Mobility Service-Registrierung](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Geben Sie unter **Konfigurationsserverdetails** die IP-Adresse und die konfigurierte Passphrase an.  

    ![Seite für die Mobility Service-Registrierung](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Klicken Sie auf **Registrieren**, um die Registrierung abzuschließen.

    ![Letzte Seite der Mobility Service-Registrierung](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Installieren des Mobilitäts-Agents über die Eingabeaufforderung

### <a name="prerequisite"></a>Voraussetzung

- Alle Serverkonfigurationen müssen in die [Unterstützungsmatrix von VMware für ein Azure-Notfallwiederherstellungsszenario](vmware-physical-azure-support-matrix.md) fallen.
- [Suchen Sie das Installationsprogramm](#locate-installer-files) basierend auf dem Betriebssystem des Servers.

### <a name="on-a-windows-machine"></a>Auf einem Windows-Computer

- Kopieren Sie das Installationsprogramm in einen lokalen Ordner (z.B. „C:\Temp“) auf dem Server, den Sie schützen möchten.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Führen Sie die Installation auf folgende Weise aus:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registrieren Sie den Agent beim Konfigurationsserver.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Installationseinstellungen
**Einstellung** | **Details**
--- | ---
Verwendung | UnifiedAgent.exe /Role <MS/MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
Setupprotokolle | Unter „%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log“.
/Role | Obligatorischer Installationsparameter. Gibt an, ob der Mobilitätsdienst (Mobility Service, MS) oder das Masterziel (Master Target, MT) installiert werden soll.
/InstallLocation| Dieser Parameter ist optional. Gibt den Installationspfad des Mobilitätsdiensts an (beliebiger Ordner).
/Platform | Obligatorisch. Gibt die Plattform an, auf der Mobility Service installiert wird. **VMware** für VMware-VMs/physische VMware-Server; **Azure** für Azure-VMs.
/Silent| Optional. Gibt an, ob das Installationsprogramm im unbeaufsichtigten Modus ausgeführt werden soll.

#### <a name="registration-settings"></a>Registrierungseinstellungen
**Einstellung** | **Details**
--- | ---
Verwendung | UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Agent-Konfigurationsprotokolle | Unter „%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log“.
/CSEndPoint | Obligatorischer Parameter. Gibt die IP-Adresse des Konfigurationsservers an. Verwenden Sie eine beliebige gültige IP-Adresse.
/PassphraseFilePath |  Obligatorisch. Speicherort der Passphrase. Verwenden Sie einen beliebiger UNC- oder lokalen Dateipfad.

### <a name="on-a-linux-machine"></a>Auf einem Linux-Computer

1. Kopieren Sie das Installationsprogramm in einen lokalen Ordner (z.B. „/tmp“) auf dem Server, den Sie schützen möchten. Führen Sie an einem Terminal die folgenden Befehle aus:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Führen Sie die Installation auf folgende Weise aus:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Nach Abschluss der Installation muss Mobility Service auf dem Konfigurationsserver registriert werden. Führen Sie den folgenden Befehl aus, um Mobility Service beim Konfigurationsserver zu registrieren:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Installationseinstellungen
**Einstellung** | **Details**
--- | ---
Verwendung | ./install -d <Install Location> -r <MS/MT> -v VmWare -q
-r | Obligatorischer Installationsparameter. Gibt an, ob der Mobilitätsdienst (Mobility Service, MS) oder das Masterziel (Master Target, MT) installiert werden soll.
-d angeben, | Dieser Parameter ist optional. Gibt den Installationspfad des Mobilitätsdiensts an: /usr/local/ASR.
-v | Obligatorisch. Gibt die Plattform an, auf der Mobility Service installiert wird. **VMware** für VMware-VMs/physische VMware-Server; **Azure** für Azure-VMs.
-q | Optional. Gibt an, ob das Installationsprogramm im unbeaufsichtigten Modus ausgeführt werden soll.

#### <a name="registration-settings"></a>Registrierungseinstellungen
**Einstellung** | **Details**
--- | ---
Verwendung | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Obligatorischer Parameter. Gibt die IP-Adresse des Konfigurationsservers an. Verwenden Sie eine beliebige gültige IP-Adresse.
-P |  Obligatorisch. Der vollständige Dateipfad der Datei, in der die Passphrase gespeichert ist. Verwenden Sie einen beliebigen gültigen Ordner.

## <a name="azure-virtual-machine-agent"></a>Azure-VM-Agent

- **Virtuelle Windows-Computer:** Ab Version 9.7.0.0 des Mobilitätsdiensts wird der [Azure VM-Agent](../virtual-machines/extensions/features-windows.md#azure-vm-agent) vom Mobilitätsdienst-Installer installiert. Damit wird sichergestellt, dass die Azure-VM die Agent-Installationsvoraussetzungen für die Verwendung beliebiger VM-Erweiterungen erfüllt, wenn ein Computer ein Failover zu Azure ausführt.
- **Virtuelle Linux-Computer:** Der [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) muss nach dem Failover manuell auf der Azure-VM installiert werden.

## <a name="locate-installer-files"></a>Suchen nach den Installerdateien

Wechseln Sie auf dem Konfigurationsserver zum Ordner „%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository“. Überprüfen Sie, welches Installationsprogramm Sie für Ihr Betriebssystem benötigen. Die folgende Tabelle enthält die Installationsprogramme für alle virtuellen VMware-Computer und alle Betriebssysteme von physischen Servern. Sie können sich vor dem Start die [unterstützten Betriebssysteme](vmware-physical-azure-support-matrix.md#replicated-machines) anschauen.

**Installerdatei** | **Betriebssystem (nur 64-Bit)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS-Server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Nächste Schritte

[Einrichten der Pushinstallation für den Mobilitätsdienst](vmware-azure-install-mobility-service.md)
