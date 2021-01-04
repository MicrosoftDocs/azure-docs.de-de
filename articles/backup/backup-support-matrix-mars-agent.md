---
title: Unterstützungsmatrix für den MARS-Agent
description: Dieser Artikel enthält eine Übersicht über die Azure Backup-Unterstützung beim Sichern von Computern, auf denen der MARS-Agent (Microsoft Azure Recovery Services) ausgeführt wird.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 26a47c2648d1307d2e7da2b25455f3f036cbf32d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997238"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Supportmatrix für die Sicherung mit dem Microsoft Azure Recovery Services (MARS)-Agent

Mit dem [Azure Backup-Dienst](backup-overview.md) können Sie lokale Computer und Apps sowie virtuelle Azure-Computer (VMs) sichern. Dieser Artikel enthält eine Übersicht über die Supporteinstellungen und Einschränkungen beim Sichern von Computern mithilfe des MARS-Agents (Microsoft Azure Recovery Services).

## <a name="the-mars-agent"></a>Der MARS-Agent

Der MARS-Agent wird von Azure Backup verwendet, um Daten von lokalen Computern und Azure-VMs in einem Backup Recovery Services-Tresor in Azure zu sichern. Mit dem MARS-Agent haben Sie folgende Möglichkeiten:

- Sie können den Agent auf lokalen Windows-Computern ausführen, sodass diese direkt in einem Backup Recovery Services-Tresor in Azure gesichert werden können.
- Sie können den Agent auf Windows-VMs ausführen, sodass diese direkt in einem Tresor gesichert werden können.
- Sie können den Agent auf einem MABS-Server (Microsoft Azure Backup Server) oder System Center DPM-Server (Data Protection Manager) ausführen. In diesem Szenario werden Computer und Workloads auf einem MABS- oder DPM-Server gesichert. Anschließend wird dieser Server vom MARS-Agent in einem Tresor in Azure gesichert.

> [!NOTE]
>Azure Backup unterstützt keine automatische Sommerzeitanpassung der Uhr. Ändern Sie die Richtlinie, um sicherzustellen, dass die Sommerzeit für das Konto übernommen wird, um Diskrepanzen zwischen der tatsächlichen Uhrzeit und der geplanten Uhrzeit für die Sicherung zu vermeiden.

Welche Möglichkeiten zur Sicherung bestehen, hängt davon ab, welcher Agent installiert ist. Weitere Informationen finden Sie unter [Architektur: Direkte Sicherung von lokalen Windows Server-Computern oder Azure-VM-Dateien oder -Ordnern](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Informationen zur Architektur für die Sicherung mit MABS und DPM finden Sie unter [Architektur: Sicherung mit DPM/MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Weitere Informationen zur Sicherungsarchitektur finden Sie auch unter den [Anforderungen](backup-support-matrix-mabs-dpm.md).

**Installation** | **Details**
--- | ---
Aktuellen MARS-Agent herunterladen | Sie können die neueste Version des Agenten aus dem Tresor herunterladen oder [direkt herunterladen](https://aka.ms/azurebackup_agent).
Direkt auf dem Computer installieren | Sie können den MARS-Agent direkt auf einem lokalen Windows-Server oder einem virtuellen Windows-Computer installieren, auf dem eines der [unterstützten Betriebssysteme](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) ausgeführt wird.
Auf einem Sicherungsserver installieren | Wenn Sie DPM oder MABS für die Sicherung auf Azure einrichten, laden Sie den MARS-Agent herunter und installieren ihn auf dem Server. Sie können den Agent gemäß den [unterstützten Betriebssystemen](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) in der Supportmatrix des Sicherungsservers installieren.

> [!NOTE]
> Standardmäßig verfügen Azure-VMs, die für die Sicherung aktiviert sind, über eine Azure Backup-Erweiterungsinstallation. Diese Erweiterung sichert die gesamte VM. Sie können den MARS-Agent auf einer Azure-VM neben der Erweiterung installieren und ausführen, wenn Sie nur bestimmte Ordner und Dateien und nicht die gesamte VM sichern möchten.
> Wenn Sie den MARS-Agent auf einer Azure-VM ausführen, werden Dateien oder Ordner gesichert, die sich im temporären Speicher auf der VM befinden. Sicherungen schlagen fehl, wenn die Dateien oder Ordner aus dem temporären Speicher entfernt werden oder wenn der temporäre Speicher entfernt wird.

## <a name="cache-folder-support"></a>Unterstützung für Cacheordner

Wenn Sie Daten mit dem MARS-Agent sichern, wird eine Momentaufnahme der Daten erstellt und in einem lokalen Cacheordner speichert, bevor die Daten vom Agent an Azure gesendet werden. Für den Cacheordner (neu) gelten verschiedene Anforderungen:

**Cache** | **Details**
--- | ---
Size |  Der freie Speicherplatz im Cacheordner muss mindestens 5 bis 10 Prozent der Gesamtgröße Ihrer Sicherungsdaten betragen.
Position | Der Cacheordner muss lokal auf dem Computer gespeichert werden, der gesichert wird, und er muss online sein. Der Cacheordner darf sich nicht in einer Netzwerkfreigabe, auf Wechselmedien oder in einem Offline-Volume befinden.
Ordner | Der Cacheordner darf auf einem deduplizierten Volume oder in einem Ordner, der komprimiert ist, eine geringe Dichte aufweist oder einen Analysepunkt hat, nicht verschlüsselt sein.
Andere Speicherorte | Sie können den Cachespeicherort ändern, indem Sie die Sicherungs-Engine beenden (`net stop bengine`) und den Cacheordner in ein neues Laufwerk kopieren. (Stellen Sie sicher, dass genügend Platz vorhanden ist.) Dann aktualisieren Sie zwei Registrierungseinträge unter **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** und **Config/CloudBackupProvider/ScratchLocation**) auf den neuen Speicherort und starten die Engine neu.

## <a name="networking-and-access-support"></a>Netzwerk und Zugriffsunterstützung

### <a name="url-and-ip-access"></a>Zugriff auf URLs und IP-Adressen

Der MARS-Agent benötigt Zugriff auf diese URLs:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

Und auf diese IP-Adressen:

- 20.190.128.0/18
- 40.126.0.0/18

Beim Zugriff auf alle oben aufgeführten URLs und IP-Adressen wird das HTTPS-Protokoll auf Port 443 verwendet.

Beim Sichern von Dateien und Ordnern von Azure-VMs mit dem MARS-Agent muss das virtuelle Azure-Netzwerk ebenfalls so konfiguriert werden, dass es Zugriff zulässt. Wenn Sie Netzwerksicherheitsgruppen (NSG) verwenden, können Sie den ausgehenden Zugriff auf Azure Backup mithilfe des Diensttags *AzureBackup* zulassen. Zusätzlich zum Tag „Azure Backup“ müssen Sie auch Konnektivität für Authentifizierung und Datenübertragung zulassen, indem Sie ähnliche [NSG-Regeln](../virtual-network/network-security-groups-overview.md#service-tags) für Azure AD (*AzureActiveDirectory*) und Azure Storage (*Storage*) erstellen. Die folgenden Schritte beschreiben das Vorgehen zum Erstellen einer Regel für das Azure Backup-Tag:

1. Navigieren Sie unter **Alle Dienste** zu **Netzwerksicherheitsgruppen**, und wählen Sie die Netzwerksicherheitsgruppe aus.
2. Wählen Sie unter **Einstellungen** die Option **Ausgangssicherheitsregeln** aus.
3. Wählen Sie **Hinzufügen**. Geben Sie die erforderlichen Informationen zum Erstellen einer neuen Regel ein, wie unter [Einstellungen zu Sicherheitsregeln](../virtual-network/manage-network-security-group.md#security-rule-settings) beschrieben. Stellen Sie sicher, dass die Option **Ziel** auf *Diensttag* und **Zieldiensttag** auf *AzureBackup* festgelegt wurde.
4. Wählen Sie **Hinzufügen** aus, um die neu erstellte Ausgangssicherheitsregel zu speichern.

Auf ähnliche Weise können Sie ausgehende NSG-Sicherheitsregeln für Azure Storage und Azure AD erstellen. Weitere Informationen zu Diensttags finden Sie in [diesem Artikel](../virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-Unterstützung

Sie können Ihre Daten über Azure ExpressRoute mit öffentlichem Peering (verfügbar für alte Verbindungen) und Microsoft-Peering sichern. Die Sicherung über privates Peering wird nicht unterstützt.

Bei öffentlichem Peering: Stellen Sie den Zugriff auf die folgenden Domänen/Adressen sicher:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Mit Microsoft-Peering: Wählen Sie die folgenden Dienste, Regionen und relevanten Communitywerte aus:

- Azure Backup (entsprechend dem Standort Ihres Recovery Services-Tresors)
- Azure Active Directory (12076:5060)
- Azure Storage (entsprechend dem Standort Ihres Recovery Services-Tresors)

Weitere Informationen finden Sie unter [ExpressRoute-Routinganforderungen](../expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Öffentliches Peering gilt für neue Leitungen als veraltet.

### <a name="private-endpoint-support"></a>Unterstützung für den privaten Endpunkt

Sie können nun private Endpunkte verwenden, um Ihre Daten von Servern sicher in Ihrem Recovery Services-Tresor zu sichern. Da Azure Active Directory derzeit keine privaten Endpunkte unterstützt, muss für die IPs und FQDNs, die für Azure Active Directory erforderlich sind, ausgehender Zugriff separat zugelassen werden.

Wenn Sie den MARS-Agent zur Sicherung Ihrer lokalen Ressourcen verwenden, stellen Sie sicher, dass Ihr lokales Netzwerk (das die zu sichernden Ressourcen enthält) ein Peering mit dem virtuellen Azure-Netzwerk aufweist, das einen privaten Endpunkt für den Tresor enthält. Sie können dann mit der Installation des MARS-Agents fortfahren und die Sicherung konfigurieren. Sie müssen jedoch sicherstellen, dass die gesamte Kommunikation für die Sicherung ausschließlich über das Peeringnetzwerk erfolgt.

Wenn Sie private Endpunkte für den Tresor entfernen, nachdem ein MARS-Agent bei ihm registriert wurde, müssen Sie den Container erneut beim Tresor registrieren. Sie müssen den Schutz für sie nicht aufheben.

Weitere Informationen zu privaten Endpunkten für Azure Backup finden Sie [hier](private-endpoints.md).

### <a name="throttling-support"></a>Unterstützung für Drosselung

**Feature** | **Details**
--- | ---
Bandbreitensteuerung | Unterstützt. Verwenden Sie im MARS-Agent **Eigenschaften ändern**, um die Bandbreite anzupassen.
Netzwerkdrosselung | Nicht verfügbar für gesicherte Computer, auf denen Windows Server 2008 R2, Windows Server 2008 SP2 oder Windows 7 ausgeführt wird.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

>[!NOTE]
> Der MARS-Agent unterstützt Windows Server Core-SKUs nicht.

Mit dem MARS-Agent können Sie unter den unten aufgeführten Betriebssystemen, die auf folgenden Computern ausgeführt werden, direkt in Azure sichern:

1. Lokale Windows-Server
2. Azure-VMs unter Windows

Dabei muss es sich um 64-Bit-Betriebssysteme handeln, die mit den neuesten Service Packs und Updates ausgeführt werden müssen. Eine Übersicht über diese Betriebssysteme finden in der folgenden Tabelle:

**Betriebssystem** | **Dateien/Ordner** | **Systemstatus** | **Software-/Modulanforderungen**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Nein |  Überprüfen der entsprechenden Serverversion auf Software-/Modulanforderungen
Windows 8.1 (Enterprise, Pro)| Ja |Nein | Überprüfen der entsprechenden Serverversion auf Software-/Modulanforderungen
Windows 8 (Enterprise, Pro) | Ja | Nein | Überprüfen der entsprechenden Serverversion auf Software-/Modulanforderungen
Windows Server 2016 (Standard, Datacenter, Essentials) | Ja | Ja | – .NET 4.5 <br> – Windows PowerShell <br> – Neuestes kompatibles Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ja | Ja | – .NET 4.5 <br> – Windows PowerShell <br> – Neuestes kompatibles Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Ja | Ja |– .NET 4.5 <br> – Windows PowerShell <br> – Neuestes kompatibles Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0 <br> – Abbildverwaltung für die Bereitstellung (Deployment Image Servicing and Management (DISM.exe))
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Ja | Nein | – .NET 4.5 <br> – Windows PowerShell <br> – Neuestes kompatibles Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Ja | Ja | – .NET 4.5 <br> – Windows PowerShell <br> – Neuestes kompatibles Microsoft VC++ Redistributable <br> – Microsoft Management Console (MMC) 3.0

Weitere Informationen finden Sie unter [Unterstützte MABS- und DPM-Betriebssysteme](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Ablauf des Supportzeitraums für Betriebssysteme

Für die folgenden Betriebssysteme ist der Supportzeitraum abgelaufen. Wir empfehlen Ihnen daher dringend, ein Betriebssystemupgrade durchzuführen, damit Sie weiter geschützt sind.

Falls bestehende Verpflichtungen ein Upgrade des Betriebssystems verhindern sollten, können Sie eine Migration der Windows-Server zu Azure-VMs erwägen und Azure-VM-Sicherungen nutzen, um den Schutz aufrechtzuerhalten. Weitere Informationen zur Migration Ihrer Windows-Server finden Sie auf [dieser Seite zur Migration](https://azure.microsoft.com/migration/windows-server/).

Für lokale oder gehostete Umgebungen, für die ein Upgrade des Betriebssystems oder die Migration zu Azure nicht möglich ist, sollten Sie „Erweiterte Sicherheitsupdates“ für die Computer aktivieren, damit für Schutz und Support gesorgt ist. Beachten Sie hierbei, dass nur für bestimmte Editionen Anspruch auf „Erweiterte Sicherheitsupdates“ besteht. Weitere Informationen finden Sie auf der [Seite mit den häufig gestellten Fragen](https://www.microsoft.com/windows-server/extended-security-updates).

| **Betriebssystem**                                       | **Dateien/Ordner** | **Systemstatus** | **Software-/Modulanforderungen**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ja               | Nein                 | Überprüfen der entsprechenden Serverversion auf Software-/Modulanforderungen |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Ja               | Ja                | - .NET 3.5, .NET 4.5 <br>  – Windows PowerShell <br>  – Kompatibles Microsoft VC++ Redistributable <br>  – Microsoft Management Console (MMC) 3.0 <br>  – Abbildverwaltung für die Bereitstellung (Deployment Image Servicing and Management (DISM.exe)) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Ja               | Nein                 | - .NET 3.5, .NET 4.5 <br>  – Windows PowerShell <br>  – Kompatibles Microsoft VC++ Redistributable <br>  – Microsoft Management Console (MMC) 3.0 <br>  – Abbildverwaltung für die Bereitstellung (Deployment Image Servicing and Management (DISM.exe)) <br>  - Virtual Server 2005 Basis + KB KB948515 |

## <a name="backup-limits"></a>Backup-Grenzwerte

### <a name="size-limits"></a>Größenbeschränkungen

Bei Azure Backup ist die Größe einer Datei- oder Ordnerdatenquelle, die gesichert werden kann, begrenzt. Die Elemente, die von einem einzelnen Volume gesichert werden, dürfen die in der folgenden Tabelle angegebenen Größen nicht überschreiten:

**Betriebssystem** | **Größenlimit**
--- | ---
Windows Server 2012 oder höher |54.400 GB
Windows Server 2008 R2 SP1 |1\.700 GB
Windows Server 2008 SP2| 1\.700 GB
Windows 8 oder höher| 54.400 GB
Windows 7| 1\.700 GB

### <a name="minimum-retention-limits"></a>Mindestwerte für die Aufbewahrung

Im Folgenden finden Sie die minimale Aufbewahrungsdauer, die für die verschiedenen Wiederherstellungspunkte festgelegt werden kann:

|Wiederherstellungspunkt |Duration  |
|---------|---------|
|Täglicher Wiederherstellungspunkt    |   7 Tage      |
|Wöchentlicher Wiederherstellungspunkt     |    4 Wochen     |
|Monatlicher Wiederherstellungspunkt    |   3 Monate      |
|Jährlicher Wiederherstellungspunkt  |      1 Jahr   |

### <a name="other-limitations"></a>Weitere Einschränkungen

- MARS unterstützt nicht den Schutz mehrerer Computer mit demselben Namen in einem einzelnen Tresor.

## <a name="supported-file-types-for-backup"></a>Unterstützte Dateitypen für die Sicherung

**Typ** | **Unterstützung**
--- | ---
Verschlüsselt<sup>*</sup>| Unterstützt.
Compressed | Unterstützt.
Platzsparend | Unterstützt.
Komprimiert und geringe Dichte |Unterstützt.
Feste Links| Wird nicht unterstützt. Übersprungen.
Analysepunkt| Wird nicht unterstützt. Übersprungen.
Verschlüsselt und geringe Dichte |Wird nicht unterstützt. Übersprungen.
Komprimierter Stream| Wird nicht unterstützt. Übersprungen.
Platzsparender Stream| Wird nicht unterstützt. Übersprungen.
OneDrive (synchronisierte Dateien sind Streams mit geringer Dichte)| Wird nicht unterstützt.
Ordner mit aktivierter DFS-Replikation | Wird nicht unterstützt.

\* Stellen Sie sicher, dass für den MARS-Agent Zugriff auf die erforderlichen Zertifikate besteht, damit auf die verschlüsselten Dateien zugegriffen werden kann. Nicht zugängliche Dateien werden übersprungen.

## <a name="supported-drives-or-volumes-for-backup"></a>Unterstützte Laufwerke oder Volumes für die Sicherung

**Laufwerk/Volume** | **Unterstützung** | **Details**
--- | --- | ---
Schreibgeschützte Volumes| Nicht unterstützt | Der Volumeschattenkopie-Dienst (VSS) funktioniert nur, wenn das Volume beschreibbar ist.
Offlinevolumes| Nicht unterstützt |VSS funktioniert nur, wenn das Volume online ist.
Netzwerkfreigabe| Nicht unterstützt |Das Volume muss sich lokal auf dem Server befinden.
BitLocker-gesperrte Volumes| Nicht unterstützt |Das Volume muss entsperrt werden, damit die Sicherung starten kann.
Dateisystemidentifikation| Nicht unterstützt |Es wird nur NTFS unterstützt.
Wechselmedien| Nicht unterstützt |Alle Quellen für Sicherungselemente müssen ein *festes* Medium sein.
Deduplizierte Laufwerke | Unterstützt | Azure Backup konvertiert die deduplizierten Daten in reguläre Daten. Dabei werden die Daten optimiert, verschlüsselt, gespeichert und an den Tresor gesendet.

## <a name="support-for-initial-offline-backup"></a>Unterstützung für die erste Offlinesicherung

Azure Backup unterstützt *Offline-Seeding*, um erste Sicherungsdaten über Datenträger an Azure zu übertragen. Dieser Support ist hilfreich, wenn Ihre erste Sicherung wahrscheinlich im Größenbereich von Terabyte (TBs) liegt. Die Offlinesicherung wird unterstützt für:

- Direkte Sicherung von Dateien und Ordnern auf lokalen Computern, auf denen der MARS-Agent ausgeführt wird.
- Sicherung von Workloads und Dateien von einem DPM-Server oder MABS.

Die Offlinesicherung kann nicht für Systemstatusdateien verwendet werden.

## <a name="support-for-data-restoration"></a>Support für die Datenwiederherstellung

Mithilfe der Funktion [Sofortige Wiederherstellung](backup-instant-restore-capability.md) von Azure Backup können Sie Daten vor dem Kopieren in den Tresor wiederherstellen. Auf dem Computer, den Sie sichern, muss .NET Framework 4.5.2 oder höher ausgeführt werden.

Sicherungen können nicht auf einem Zielcomputer wiederhergestellt werden, auf dem eine ältere Version des Betriebssystems ausgeführt wird. So kann eine Sicherung, die von einem Computer erstellt wurde, auf dem Windows 7 ausgeführt wird, beispielsweise unter Windows 8 oder höher wiederhergestellt werden. Eine Sicherung, die von einem Computer erstellt wurde, auf dem Windows 8 ausgeführt wird, kann dagegen nicht auf einem Computer wiederhergestellt werden, auf dem Windows 7 ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Sicherungsarchitektur, für die der MARS-Agent verwendet wird](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Erfahren Sie, was unterstützt wird, wenn Sie [den MARS-Agent auf einem MABS- oder DPM-Server ausführen](backup-support-matrix-mabs-dpm.md).
