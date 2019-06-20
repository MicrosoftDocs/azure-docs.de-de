---
title: Einrichten der Notfallwiederherstellung für VMware-VMs oder physische Server an einem sekundären Standort mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren sie, wie Sie die Notfallwiederherstellung für VMware-VMs oder physische Windows- und Linux-Server an einem sekundären Standort mit Azure Site Recovery einrichten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 9a1cb63bd2a209c72af608d23515723a63b180e1
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417728"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Einrichten der Notfallwiederherstellung für lokale virtuelle VMware-Computer oder physische Server an einem sekundären Standort

InMage Scout in [Azure Site Recovery](site-recovery-overview.md) ermöglicht eine Echtzeitreplikation zwischen lokalen VMware-Standorten. InMage Scout ist Teil der Abonnements des Azure Site Recovery-Diensts.

## <a name="end-of-support-announcement"></a>Ankündigung zum Ende des Supports

Für das Azure Site Recovery-Szenario für die Replikation zwischen lokalen VMware-Instanzen oder physischen Datencentern rückt das Ende des Supportzeitraums näher.

-   Ab August 2018 kann das Szenario im Recovery Services-Tresor nicht mehr konfiguriert werden, und die InMage Scout-Software kann nicht aus dem Tresor heruntergeladen werden. Vorhandene Bereitstellungen werden unterstützt. 
-   Ab dem 31. Dezember 2020 wird das Szenario nicht mehr unterstützt.
- Vorhandene Partner können für das Szenario das Onboarding für neue Kunden bis zum Ende des Supportzeitraums durchführen.

In den Jahren 2018 und 2019 werden zwei Updates veröffentlicht: 

-   Update 7: Korrektur von Problemen in Bezug auf die Netzwerkkonfiguration und Konformität und Bereitstellung der TLS 1.2-Unterstützung.
-   Update 8: Support für Linux-Betriebssysteme, RHEL/CentOS 7.3/7.4/7.5 und SUSE 12

Nach Update 8 werden keine weiteren Updates mehr veröffentlicht. Für die in Update 8 hinzugefügten Betriebssysteme ist eingeschränkter Hotfix-Support verfügbar, und Fehlerbehebungen werden auf bestmögliche Weise bereitgestellt.

Innovationen werden für Azure Site Recovery weiter vorangetrieben, indem VMware- und Hyper-V-Kunden eine nahtlose und erstklassige DRaaS-Lösung mit Azure als Standort für die Notfallwiederherstellung erhalten. Microsoft empfiehlt bestehenden InMage/ASR Scout-Kunden, die Verwendung des Azure Site Recovery-Szenarios „VMware zu Azure“ zu erwägen, um die mit der Geschäftskontinuität verbundenen Anforderungen zu erfüllen. Das Azure Site Recovery-Szenario „VMware zu Azure“ ist eine für Unternehmen geeignete Notfallwiederherstellungslösung für VMware-Anwendungen, die RPO- und RTO-Werte im Minutenbereich, Unterstützung der Anwendungsreplikation und -wiederherstellung für mehrere VMs, nahtloses Onboarding, umfassende Überwachung und einen erheblichen Vorteil bei den Gesamtbetriebskosten bietet.

### <a name="scenario-migration"></a>Szenariomigration
Als Alternative empfehlen wir die Einrichtung der Notfallwiederherstellung für lokale VMware-VMs und physische Computer per Replikation in Azure. Gehen Sie hierzu wie folgt vor:

1.  Sehen Sie sich unten den Schnellvergleich an. Bevor Sie lokale Computer replizieren können, müssen Sie überprüfen, ob diese die [Anforderungen](./vmware-physical-azure-support-matrix.md#replicated-machines) für die Replikation in Azure erfüllen. Für die Replikation von VMware-VMs empfehlen wir Ihnen, die [Richtlinien zur Kapazitätsplanung](./site-recovery-plan-capacity-vmware.md) zu lesen und das [Bereitstellungsplaner-Tool](./site-recovery-deployment-planner.md) auszuführen, um die Kapazitätsanforderungen zu identifizieren und die Konformität zu überprüfen.
2.  Nach dem Ausführen des Bereitstellungsplaners können Sie die Replikation einrichten: o   Arbeiten Sie für VMware-VMs die Tutorials zum [Vorbereiten von Azure](./tutorial-prepare-azure.md), [Vorbereiten Ihrer lokalen VMware-Umgebung](./vmware-azure-tutorial-prepare-on-premises.md) und [Einrichten der Notfallwiederherstellung](./vmware-azure-tutorial-prepare-on-premises.md) durch.
o   Verwenden Sie für physische Computer dieses [Tutorial](./physical-azure-disaster-recovery.md).
3.  Nachdem Computer in Azure repliziert wurden, können Sie ein [Notfallwiederherstellungsverfahren](./site-recovery-test-failover-to-azure.md) als Testlauf durchführen, um sicherzustellen, dass alles wie erwartet funktioniert.

### <a name="quick-comparison"></a>Schnellvergleich

**Feature** | **Replikation in Azure** |**Replikation zwischen VMware-Datencentern**
--|--|--
**Erforderliche Komponenten** |Mobility Service auf replizierten Computern. Lokaler Konfigurationsserver, Prozessserver, Masterzielserver. Temporärer Prozessserver in Azure für Failback.|Mobility Service, Prozessserver, Konfigurationsserver und Masterziel
**Konfiguration und Orchestrierung** |Recovery Services-Tresor im Azure-Portal | Verwenden von vContinuum 
**Repliziert** |Datenträger (Windows und Linux) |Volume – Windows<br> Linux – Datenträger
**Freigegebener Datenträgercluster** |Nicht unterstützt|Unterstützt
**Grenzwerte für Datenänderungsrate (Durchschnitt)** |10 MB/s – Daten pro Datenträger<br> 25 MB/s – Daten pro VM<br> [Weitere Informationen](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s – Daten pro Datenträger  <br> > 25 MB/s – Daten pro VM
**Überwachung** |Über Azure-Portal|Über CX (Konfigurationsserver)
**Unterstützungsmatrix** | Klicken Sie [hier](./vmware-physical-azure-support-matrix.md), um ausführliche Informationen zu erhalten.|[Download: Kompatible ASR Scout-Matrix](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes:

- [Überprüfen](vmware-physical-secondary-support-matrix.md) Sie die Supportanforderungen für alle Komponenten.
- Vergewissern Sie sich, dass die zu replizierenden Computer die Anforderungen für [Unterstützung replizierter Computer](vmware-physical-secondary-support-matrix.md#replicated-vm-support) erfüllen.


## <a name="download-and-install-component-updates"></a>Herunterladen und Installieren von Komponentenupdates

 Suchen Sie nach den neuesten [Updates](#updates), und installieren Sie sie. Updates müssen auf Servern in der folgenden Reihenfolge installiert werden:

1. RX-Server (falls zutreffend)
2. Konfigurationsserver
3. Prozessserver
4. Masterzielserver
5. vContinuum-Server
6. Quellserver (sowohl Windows- als auch Linux-Server)

Installieren Sie die Updates wie folgt:

> [!NOTE]
>In der ZIP-Updatedatei haben unter Umständen nicht alle Scout-Komponenten die gleiche Dateiupdateversion. Die ältere Version gibt an, dass die Komponente seit dem vorletzten Update (also dem Update vor diesem Update) nicht geändert wurde.

Laden Sie die ZIP-Datei des [Updates](https://aka.ms/asr-scout-update7) sowie die Konfigurationsdateien für das [MySQL- und PHP-Upgrade](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) herunter. Die Update-ZIP-Datei enthält alle grundlegenden Binärdateien sowie kumulative Upgrade-Binärdateien der folgenden Komponenten: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Extrahieren Sie die ZIP-Dateien.
  2. **RX-Server**: Kopieren Sie **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** auf den RX-Server, und extrahieren Sie die Datei. Führen Sie im extrahierten Ordner **/Install**aus.
  3. **Konfigurationsserver und Prozessserver**: Kopieren Sie **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** auf den Konfigurationsserver und den Prozessserver. Doppelklicken Sie auf die Datei, um sie auszuführen.<br>
  4. **Windows-Masterzielserver**: Um den vereinheitlichten Agent zu aktualisieren, kopieren Sie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** auf den Server. Doppelklicken Sie auf die Datei, um sie auszuführen. Dieselbe Datei kann auch für eine Neuinstallation verwendet werden. Das gleiche Update für den vereinheitlichten Agent ist auch für den Quellserver anwendbar.
  Das Update muss nicht für das mit **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** vorbereitete Masterziel gültig sein, da es sich dabei um einen allgemein verfügbaren Installer mit allen aktuellen Änderungen handelt.
  5. **vContinuum-Server**:  Kopieren Sie **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** auf den Server.  Stellen Sie sicher, dass Sie den vContinuum-Assistenten geschlossen haben. Doppelklicken Sie auf die Datei, um sie auszuführen.
  6. **Linux-Masterzielserver**: Um den vereinheitlichten Agent zu aktualisieren, kopieren Sie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** auf den Linux-Masterzielserver, und extrahieren Sie die Datei. Führen Sie im extrahierten Ordner **/Install**aus.
  7. **Windows-Quellserver**: Um den vereinheitlichten Agent zu aktualisieren, kopieren Sie **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** auf den Quellserver. Doppelklicken Sie auf die Datei, um sie auszuführen. 
  8. **Linux-Quellserver**: Um den vereinheitlichten Agent zu aktualisieren, kopieren Sie die entsprechende Version der Datei des vereinheitlichten Agent auf den Linux-Server, und extrahieren Sie sie. Führen Sie im extrahierten Ordner **/Install**aus.  Beispiel: Für einen RHEL 6.7 64-Bit-Server kopieren Sie **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** auf den Server, und extrahieren Sie die Datei. Führen Sie im extrahierten Ordner **/Install**aus.
  9. Nach dem Upgrade des Konfigurationsservers, Prozessservers und RX-Servers mit der oben genannten Installationsprogrammen muss ein Upgrade der PHP- und MySQL-Bibliotheken manuell mit den in Abschnitt 7.4 der [Schnellinstallationsanleitung](https://aka.ms/asr-scout-quick-install-guide) vorgenommen werden.

## <a name="enable-replication"></a>Aktivieren der Replikation

1. Richten Sie die Replikation zwischen den Quell- und Zielstandorten von VMware ein.
2. Informationen zu Installation, Schutz und Wiederherstellung finden Sie in den folgenden Dokumenten:

   * [Versionshinweise](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitätsmatrix](https://aka.ms/asr-scout-cm)
   * [Benutzerhandbuch](https://aka.ms/asr-scout-user-guide)
   * [RX-Benutzerhandbuch](https://aka.ms/asr-scout-rx-user-guide)
   * [Schnellinstallationsanleitung](https://aka.ms/asr-scout-quick-install-guide)
   * [Upgrade von MYSQL- und PHP-Bibliotheken](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Aktualisierungen

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Update 7 
Aktualisiert: 31. Dezember 2018 [Scout-Update 7](https://aka.ms/asr-scout-update7) herunterladen.
Scout-Update 7 ist ein vollständiges Installationsprogramm, das sowohl für Neuinstallationen als auch für ein Upgrade vorhandener Agents/MT verwendet werden kann, die noch auf dem Status früherer Updates sind (von Update 1 bis Update 6). Es enthält alle Fixes aus Update 1 bis Update 6 sowie die neuen Fixes und Erweiterungen, die unten beschrieben werden.
 
#### <a name="new-features"></a>Neue Funktionen
* PCI-Compliance
* TLS v1.2-Unterstützung

#### <a name="bug-and-security-fixes"></a>Fehler- und Sicherheitskorrekturen
* Behoben: Windows-Cluster-/eigenständige Computer besitzen eine falsche IP-Konfiguration nach der Wiederherstellungs-/Notfallwiederherstellungsdrills.
* Behoben: Manchmal schlägt der Vorgang „Datenträger hinzufügen“ bei V2V-Clustern fehl.
* Behoben: Der vContinuum-Assistent bleibt während der Wiederherstellungsphase hängen, wenn das Masterziel Windows Server 2016 ist.
* Behoben: MySQL-Sicherheitsprobleme werden durch ein Upgrade von MySQL auf Version 5.7.23 behoben.

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Manuelles Upgrade für PHP und MySQL auf KS, PS und RX
Für die PHP-Skriptplattform sollte ein Upgrade auf Version 7.2.10 auf dem Konfigurationsserver, Prozessserver und RX-Server vorgenommen werden.
Für das MySQL-Datenbankverwaltungssystem sollte ein Upgrade auf Version 5.7.23 auf dem Konfigurationsserver, Prozessserver und RX-Server vorgenommen werden.
Befolgen Sie für ein Upgrade von PHP- und MySQL-Versionen die manuellen Schritte in der [Schnellinstallationsanleitung](https://aka.ms/asr-scout-quick-install-guide).

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Aktualisiert: 12. Oktober 2017

Laden Sie [Scout Update 6](https://aka.ms/asr-scout-update6) herunter.

Scout Update 6 ist ein kumulatives Update. Es enthält sämtliche Fixes aus Update 1 bis Update 5 sowie die neuen Fixes und Verbesserungen, die unten beschrieben werden. 

#### <a name="new-platform-support"></a>Unterstützung für die neue Plattform
* Unterstützung für die Windows Server 2016-Quellinstanz hinzugefügt
* Unterstützung für die folgenden Linux-Betriebssysteme wurde hinzugefügt:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Unterstützung für VMware Center 6.5 hinzugefügt

Installieren Sie die Updates wie folgt:

> [!NOTE]
>In der ZIP-Updatedatei haben unter Umständen nicht alle Scout-Komponenten die gleiche Dateiupdateversion. Die ältere Version gibt an, dass die Komponente seit dem vorletzten Update (also dem Update vor diesem Update) nicht geändert wurde.

Laden Sie die ZIP-Datei für das [Update](https://aka.ms/asr-scout-update6) herunter. Die Datei enthält die folgenden Komponenten: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4-Bits für RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extrahieren Sie die ZIP-Dateien.
  2. **RX-Server**: Kopieren Sie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** auf den RX-Server, und extrahieren Sie die Datei. Führen Sie im extrahierten Ordner **/Install**aus.
  3. **Konfigurationsserver und Prozessserver**: Kopieren Sie **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** auf den Konfigurationsserver und den Prozessserver. Doppelklicken Sie auf die Datei, um sie auszuführen.<br>
  4. **Windows-Masterzielserver**: Um den vereinheitlichten Agent zu aktualisieren, kopieren Sie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** auf den Server. Doppelklicken Sie auf die Datei, um sie auszuführen. Das gleiche Update für den vereinheitlichten Agent ist auch für den Quellserver anwendbar. Wenn die Quelle nicht auf Update 4 aktualisiert wurde, müssen Sie den vereinheitlichten Agent aktualisieren.
  Das Update muss nicht auf das mit **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** vorbereitete Masterziel angewendet werden, da es sich dabei um einen allgemein verfügbaren Installer mit allen aktuellen Änderungen handelt.
  5. **vContinuum-Server**:  Kopieren Sie **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** auf den Server.  Stellen Sie sicher, dass Sie den vContinuum-Assistenten geschlossen haben. Doppelklicken Sie auf die Datei, um sie auszuführen.
  Das Update muss nicht auf das mit **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** vorbereitete Masterziel angewendet werden, da es sich dabei um einen allgemein verfügbaren Installer mit allen aktuellen Änderungen handelt.
  6. **Linux-Masterzielserver**: Um den vereinheitlichten Agent zu aktualisieren, kopieren Sie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** auf den Masterzielserver, und extrahieren Sie die Datei. Führen Sie im extrahierten Ordner **/Install**aus.
  7. **Windows-Quellserver**: Um den einheitlichen Agent zu aktualisieren, kopieren Sie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** auf den Quellserver. Doppelklicken Sie auf die Datei, um sie auszuführen. 
  Der Update 5-Agent muss nicht auf dem Quellserver installiert werden, wenn dieser bereits auf Update 4 aktualisiert wurde oder wenn der Quell-Agent mit dem aktuellen Base Installer **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** installiert wird.
  8. **Linux-Quellserver**: Um den vereinheitlichten Agent zu aktualisieren, kopieren Sie die entsprechende Version der Datei des vereinheitlichten Agent auf den Linux-Server, und extrahieren Sie sie. Führen Sie im extrahierten Ordner **/Install**aus.  Beispiel: Kopieren Sie für den RHEL 6.7 64-Bit-Server die Datei **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** auf den Server, und extrahieren Sie sie. Führen Sie im extrahierten Ordner **/Install**aus.


> [!NOTE]
> * Der Base Installer für den vereinheitlichten Agent für Windows wurde aktualisiert, um Windows Server 2016 zu unterstützen. Der neue Installer **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** ist im GA-Basispaket für Scout (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**) enthalten. Der gleiche Installer wird für alle unterstützten Windows-Version verwendet. 
> * Der Base Installer für Windows vContinuum und das Masterziel wurde aktualisiert, um Windows Server 2016 zu unterstützen. Der neue Installer **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** ist im GA-Basispaket für Scout (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**) enthalten. Der gleiche Installer wird zum Bereitstellen des Windows 2016-Masterziels und des Windows 2012R2-Masterziels verwendet.
> * Windows Server 2016 auf physischem Server wird von ASR Scout nicht unterstützt. Es wird nur die Windows Server 2016 VMware-VM unterstützt. 
>

#### <a name="bug-fixes-and-enhancements"></a>Programmfehlerbehebungen und Verbesserungen
- Fehler beim Failbackschutz für virtuelle Linux-Computer: Die Liste der zu replizierenden Datenträger ist am Ende der Konfiguration leer.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Scout Update 5 ist ein kumulatives Update. Es enthält sämtliche Fixes aus Update 1 bis Update 4 sowie die neuen Fixes, die unten beschrieben werden.
- Fixes aus Site Recovery Scout Update 4 bis Update 5 sind speziell für die Masterziel- und vContinuum-Komponenten bestimmt.
- Wenn auf dem Quellserver, dem Masterziel-, Konfigurations-, Prozess- und RX-Server bereits Update 4 ausgeführt wird, wenden Sie es lediglich auf den Masterzielserver an. 

#### <a name="new-platform-support"></a>Unterstützung für die neue Plattform
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 Bit **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** ist im Scout GA-Basispaket (**InMage_Scout_Standard_8.0.1 GA.zip**) enthalten. Laden Sie das GA-Paket aus dem Portal herunter; dieser Vorgang wird im Abschnitt zum Erstellen eines Tresors beschrieben.


#### <a name="bug-fixes-and-enhancements"></a>Programmfehlerbehebungen und Verbesserungen

* Fehlerbehebungen für größere Zuverlässigkeit der Windows-Clusterunterstützung:
    * Behoben: Nach der Wiederherstellung werden einige der P2V MSCS-Clusterdatenträger RAW.
    * Behoben: Die P2V MSCS-Clusterwiederherstellung schlägt aufgrund eines Konflikts bei der Datenträgerreihenfolge fehl.
    * Behoben: Der MSCS-Clustervorgang zum Hinzufügen von Datenträgern schlägt aufgrund eines Konflikts der Datenträgergrößen fehl.
    * Behoben: Bei der Größenprüfung schlägt die Prüfung der Zuordnungsfähigkeit des Quell-MSCS-Clusters zu den RDM LUNs fehl.
    * Behoben: Der Schutz für Einzelknotencluster schlägt aufgrund eines Konflikts bei der SCSI-Zuordnung fehl. 
    * Behoben: Der erneute Schutz des P2V-Windows-Clusterservers schlägt fehl, wenn Zielclusterdatenträger vorhanden sind. 
    
* Behoben: Wenn sich beim Failback-Schutz der ausgewählte Masterzielserver nicht auf demselben ESXi-Server wie der geschützte Quellcomputer befindet (während des zukunftsgerichteten Schutzes), wählt vContinuum bei der Failback-Wiederherstellung den falschen Masterzielserver aus, und die Wiederherstellung schlägt fehl.

> [!NOTE]
> * Die P2V-Cluster-Fixes sind nur auf physische MSCS-Cluster anwendbar, die kürzlich mit Site Recovery Scout Update 5 geschützt wurden. Befolgen Sie beim Installieren der Cluster-Fixes auf geschützten P2V MSCS-Clustern mit älteren Updates die Upgrade-Schritte, die in Abschnitt 12 der [Versionshinweise zu Site Recovery Scout](https://aka.ms/asr-scout-release-notes) beschrieben sind.
> * Wenn beim erneuten Schützen dieselben Datenträger auf den einzelnen Datenknoten aktiv sind wie zum Zeitpunkt des ursprünglichen Schützens, können beim erneuten Schützen eines physischen MSCS-Clusters nur vorhandene Zieldatenträger verwendet werden. Andernfalls müssen Sie die Abschnitt 12 der [Versionshinweise zu Site Recovery Scout](https://aka.ms/asr-scout-release-notes) beschriebenen Schritte ausführen, um die Zieldatenträger in den korrekten Datenspeicherpfad zu verschieben und sie während des erneuten Schützens erneut verwenden zu können. Wenn Sie den MSCS-Cluster im P2V-Modus ohne Befolgen der Upgrade-Schritte erneut schützen, wird auf dem ESXi-Zielserver ein neuer Datenträger erstellt. Die alten Datenträger müssen manuell aus dem Datenspeicher gelöscht werden.
> * Wenn ein SLES11- oder SLES11-Quellserver (mit einem beliebigen Service Pack) ordnungsgemäß neu gestartet wird, markieren Sie die Replikationspaare des **Stammdatenträgers** manuell für die erneute Synchronisierung. Es erfolgt keine Benachrichtigung über die CX-Schnittstelle. Wenn Sie den Stammdatenträger nicht für die erneute Synchronisierung markieren, treten u.U. Probleme mit der Datenintegrität auf.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 ist ein kumulatives Update. Es enthält sämtliche Fixes aus Update 1 bis Update 3 sowie die neuen Fixes, die unten beschrieben werden.

#### <a name="new-platform-support"></a>Unterstützung für die neue Plattform

* Unterstützung für vCenter/vSphere 6.0, 6.1 und 6.2 hinzugefügt
* Unterstützung für die folgenden Linux-Betriebssysteme wurde hinzugefügt:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 und 7.2
  * CentOS 7.0, 7.1 und 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 Bit **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** ist im Scout GA-Basispaket **InMage_Scout_Standard_8.0.1 GA.zip** enthalten. Laden Sie das Scout GA-Paket aus dem Portal herunter; dieser Vorgang wird im Abschnitt zum Erstellen eines Tresors beschrieben.

#### <a name="bug-fixes-and-enhancements"></a>Programmfehlerbehebungen und Verbesserungen

* Die Behandlung des Herunterfahrens wurde für die folgenden Linux-Betriebssysteme und Klone verbessert, um unerwünschte Probleme bei der erneuten Synchronisierung zu verhindern:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Für Linux sind alle Berechtigungen für den Ordnerzugriff bei der Installation des vereinheitlichten Agents jetzt ausschließlich auf den lokalen Benutzer beschränkt.
* Ein Fix für ein Zeitüberschreitungsproblem unter Windows, das beim Ausgeben von gängigen Lesezeichen für verteilte Konsistenz in stark ausgelasteten verteilten Anwendungen wie SQL Server und Share Point-Cluster aufgetreten ist.
* Ein protokollbezogener Fix im Base Installer des Konfigurationsservers.
* Ein Downloadlink für VMware vCLI 6.0 wurde dem Windows-Masterziel-Base Installer hinzugefügt.
* Zusätzliche Prüfungen und Protokolle wurden für Netzwerkkonfigurationsänderungen während Failover und Notfallwiederherstellung hinzugefügt.
* Ein Fix für ein Problem, das bewirkte, dass Aufbewahrungsinformationen nicht an den Konfigurationsserver übermittelt werden.  
* Für physische Cluster ein Fix für ein Problem, welches das Fehlschlagen der Größenänderung von Volumes im vContinuum-Assistenten bewirkte, wenn der Quelldatenträger verkleinert wurde.
* Ein Fix für ein Problem beim Clusterschutz mit dem folgenden Fehler: „Failed to find the disk signature“ (Datenträgersignatur nicht gefunden), wenn es sich beim Clusterdatenträger um einen PRDM-Datenträger handelt.
* Ein Fix für einen Absturz des cxps-Transportservers aufgrund einer Ausnahme vom Typ „Außerhalb des Bereichs“.
* Auf der Seite für die **Push-Installation** des vContinuum-Assistenten kann nun die Größe der Spalten für Servername und IP-Adresse geändert werden.
* RX-API-Verbesserungen:
  * Die fünf letzten verfügbaren allgemeinen Konsistenzpunkte werden jetzt bereitgestellt (nur garantierte Tags).
  * Für alle geschützten Geräte werden Details zu Kapazität und freiem Speicherplatz angegeben.
  * Der Scout-Treiberstatus auf dem Quellserver ist verfügbar.

> [!NOTE]
> * Inhalt des **InMage_Scout_Standard_8.0.1_GA.zip**-Basispakets:
>     * Ein aktualisierter Base Installer für den Konfigurationsserver (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Ein Windows-Masterziel-Base Installer (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Verwenden Sie für alle neuen Installationen die neuen Konfigurationsserver- und Windows-Masterziel-GA-Bits.
> * Update 4 kann direkt auf 8.0.1 GA angewendet werden.
> * Für die Updates für den Konfigurationsserver und RX kann kein Rollback durchgeführt werden, nachdem sie angewendet wurden.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Alle Site Recovery-Updates sind kumulativ. Update 3 enthält alle Fixes aus Update 1 und Update 2. Update 3 kann direkt auf 8.0.1 GA angewendet werden. Für die Updates für den Konfigurationsserver und RX kann kein Rollback durchgeführt werden, nachdem sie angewendet wurden.

#### <a name="bug-fixes-and-enhancements"></a>Programmfehlerbehebungen und Verbesserungen
Mit Update 3 werden die folgenden Probleme behoben:

* Der Konfigurationsserver und RX werden nicht im Tresor registriert, wenn sie sich hinter dem Proxy befinden.
* Die Anzahl der Stunden, in denen der RPO-Wert (Recovery Point Objective) nicht erreicht wurde, wird im Integritätsbericht nicht aktualisiert.
* Der Konfigurationsserver wird nicht mit RX synchronisiert, wenn die ESX-Hardwaredetails oder die Netzwerkdetails ein UTF-8-Zeichen enthalten.
* Windows Server 2008 R2-Domänencontroller werden nach der Wiederherstellung nicht gestartet.
* Die Offlinesynchronisierung funktioniert nicht wie erwartet.
* Nach einem VM-Failover wird das Löschen von Replikationspaaren in der Konsole des Konfigurationsservers für einen langen Zeitraum nicht fortgesetzt. Benutzer können den Failback nicht abschließen bzw. den Betrieb nicht fortsetzen.
* Momentaufnahmen-Gesamtvorgänge im Rahmen des Konsistenzauftrags wurden optimiert, um zu verhindern, dass Verbindungen von Anwendungen (z.B. SQL Server-Clients) getrennt werden.
* Die Leistung des Konsistenztools (VACP.exe) wurde verbessert. Der erforderliche Speicherbedarf beim Erstellen von Momentaufnahmen unter Windows wurde verringert.
* Der Dienst für die Pushinstallation stürzt ab, wenn das Kennwort länger als 16 Zeichen ist.
* vContinuum führt keine Prüfung auf und Abfrage neuer vCenter-Anmeldeinformationen aus, wenn Anmeldeinformationen geändert werden.
* Unter Linux lädt der Masterziel-Cache-Manager (cachemgr) keine Dateien vom Prozessserver herunter. Dies führt zu einer Drosselung des Replikationspaars.
* Wenn die Datenträgerreihenfolge des physischen Failoverclusters (MSCS) nicht auf allen Knoten gleich ist, wird die Replikation für einige Cluster-Volumes nicht festgelegt. Der Cluster muss erneut geschützt werden, damit Sie von diesem Fix profitieren können.  
* Die SMTP-Funktionalität verhält sich nicht erwartungsgemäß, nachdem RX von Scout 7.1 auf Scout 8.0.1 aktualisiert wurde.
* Dem Protokoll wurden für den Rollbackvorgang weitere Statistiken hinzugefügt, damit verfolgt wird, wie viel Zeit bis zum Abschluss des Vorgangs benötigt wird.
* Unterstützung für Linux-Betriebssysteme auf dem Quellserver wurde hinzugefügt:
  * Red Hat Enterprise Linux (RHEL) 6 Update 7
  * CentOS 6 Update 7
* In den Konfigurationsserver- und RX-Konsolen werden nun Benachrichtigungen für das Paar angezeigt, das in den Bitmap-Modus wechselt.
* Die folgenden Sicherheitsfixes wurden in RX hinzugefügt:
    * Autorisierungsumgehung per Parametermanipulation: Der Zugriff auf unzulässige Benutzer wurde beschränkt.
    * Websiteübergreifende Anforderungsfälschung: Das Konzept des Seiten-Tokens wurde implementiert, und es wird nach dem Zufallsprinzip für jede Seite generiert. Das heißt, es gibt für einen Benutzer nur eine einzige Anmeldeinstanz, und die Seitenaktualisierung funktioniert nicht. Stattdessen erfolgt eine Umleitung zum Dashboard.
    * Hochladen bösartiger Dateien: Dateien sind auf bestimmte Erweiterungen beschränkt: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml und zip.
    * Persistentes websiteübergreifendes Scripting: Eingabevalidierungen wurden hinzugefügt.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)

Fixes in Update 2 umfassen Folgendes:

* **Konfigurationsserver**: Probleme, die die korrekte Funktion der kostenlosen 31-Tage-Messfunktion verhindert haben, wenn der Konfigurationsserver im Azure Site Recovery-Tresor registriert wurde.
* **Vereinheitlichter Agent**: Behebung eines Problems in Update 1, das die Installation des Updates auf dem Masterzielserver verhinderte, wenn ein Upgrade von Version 8.0 auf 8.0.1 durchgeführt wurde.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 enthält die folgenden Fehlerbehebungen und neuen Features:

* 31 Tage lang kostenloser Schutz pro Serverinstanz. Dies ermöglicht es Ihnen, die Funktionalität zu testen oder eine Machbarkeitsstudie einzurichten.
* Alle Vorgänge auf dem Server, einschließlich von Failover und Failback, sind die ersten 31 Tage kostenlos. Der Zeitraum beginnt, wenn ein Server erstmalig mit Site Recovery Scout geschützt wird. Ab dem 32. Tag wird für jeden geschützten Server die Standardinstanzgebühr für den Site Recovery-Schutz für eine Website im Besitz des Kunden in Rechnung gestellt.
* Die Anzahl der geschützten Server, für die derzeit Gebühren berechnet werden, wird im **Dashboard** im Tresor angezeigt.
* Unterstützung für die vSphere-Befehlszeilenschnittstelle (vCLI) 5.5 Update 2 wurde hinzugefügt.
* Unterstützung für die folgenden Linux-Betriebssysteme auf dem Quellserver wurde hinzugefügt:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Fehlerbehebungen für folgende Probleme:
  * Fehlgeschlagene Tresorregistrierung für den Konfigurationsserver oder den RX-Server.
  * Clustervolumes werden nicht wie erwartet angezeigt, wenn gruppierte VMs beim Fortsetzen erneut geschützt werden.
  * Failbackfehler, wenn der Masterzielserver auf einem anderen ESXi-Server als den lokalen Produktions-VMs gehostet wird.
  * Die Konfigurationsdateiberechtigungen werden geändert, wenn Sie das Upgrade auf 8.0.1 durchführen. Diese Änderung wirkt sich auf den Schutz und die Vorgänge aus.
  * Der Schwellenwert für die Neusynchronisierung wird nicht wie erwartet erzwungen; dies bewirkt ein inkonsistentes Replikationsverhalten.
  * Die RPO-Einstellungen werden nicht korrekt in der Konfigurationsserverkonsole angezeigt. Für den unkomprimierten Datenwert wird fälschlicherweise der komprimierte Wert angezeigt.
  * Beim Vorgang „Entfernen“ wird das Löschen im vContinuum-Assistenten nicht wie erwartet durchgeführt, und die Replikation wird in der Konfigurationsserverkonsole nicht gelöscht.
  * Im vContinuum-Assistenten wird der Datenträger automatisch deaktiviert, wenn Sie in der Datenträgeransicht während des Schutzes von MSCS-VMs auf **Details** klicken.
  * Beim Szenario „physisch zu virtuell“ (P2V) werden erforderliche HP-Dienste (z.B. CIMnotify und CqMgHost) bei der VM-Wiederherstellung nicht auf manuell umgestellt. Dieses Problem bewirkt, dass sich die Startdauer verlängert.
  * Beim Schützen einer Linux-VM tritt ein Fehler auf, wenn auf dem Masterzielserver mehr als 26 Datenträger vorhanden sind.

