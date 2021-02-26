---
title: MABS & System Center DPM-Unterstützungsmatrix
description: Dieser Artikel enthält eine Zusammenfassung der Unterstützung für Azure Backup bei Verwendung von Microsoft Azure Backup Server (MABS) oder System Center DPM zum Sichern von lokalen Ressourcen und Ressourcen auf virtuellen Azure-Computern.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: aaa68dba0bbd1f3f5ffb5480a2bdb0a48ae85656
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986055"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Unterstützungsmatrix für die Sicherung mit Microsoft Azure Backup Server oder System Center DPM

Mit dem [Azure Backup-Dienst](backup-overview.md) können Sie lokale Computer und Workloads sowie virtuelle Azure-Computer (VMs) sichern. In diesem Artikel werden die Unterstützungseinstellungen und Einschränkungen für die Sicherung von Computern mithilfe von Microsoft Azure Backup Server (MABS) oder System Center Data Protection Manager (DPM) und Azure Backup zusammenfassend dargestellt.

## <a name="about-dpmmabs"></a>Informationen zu DPM/MABS

[System Center DPM](/system-center/dpm/dpm-overview) ist eine Unternehmenslösung, mit der die Sicherung und Wiederherstellung von Computern und Daten für Unternehmen konfiguriert, erleichtert und verwaltet werden. Diese Lösung ist Teil der [System Center](https://www.microsoft.com/system-center/pricing)-Produktsuite.

MABS ist ein Serverprodukt, mit dem lokale physische Server, VMs und die darauf ausgeführten Anwendungen gesichert werden können.

MABS basiert auf System Center DPM und bietet ähnliche Funktionen mit einigen Unterschieden:

- Für die Ausführung von MABS ist keine Lizenz für System Center erforderlich.
- In Azure ist für MABS und für DPM die langfristige Sicherungsspeicherung möglich. Darüber hinaus können Sie mit DPM Daten zur langfristigen Speicherung auf Band sichern. Diese Funktion ist in MABS nicht enthalten.
- [Sie können einen primären DPM-Server mit einem sekundären DPM-Server sichern.](/system-center/dpm/back-up-the-dpm-server) Der sekundäre Server schützt die primäre Serverdatenbank und die auf dem primären Server gespeicherten Datenquellenreplikate. Wenn der primäre Server ausfällt, kann der sekundäre Server die vom primären Server geschützten Workloads weiterhin schützen, bis der primäre Server wieder verfügbar ist.  Diese Funktion ist in MABS nicht enthalten.

Sie können MABS über das [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57520) herunterladen. Es kann lokal oder auf einem virtuellen Azure-Computer ausgeführt werden.

DPM und MABS unterstützen eine Vielzahl von Anwendungen und Server- und Clientbetriebssystemen. Sie ermöglichen mehrere Sicherungsszenarien:

- Sie können Daten auf Computerebene mit Systemstatussicherung oder Bare-Metal-Sicherung sichern.
- Sie können bestimmte Volumes, Freigaben, Ordner und Dateien sichern.
- Sie können bestimmte Apps über optimierte App-bezogene Einstellungen sichern.

## <a name="dpmmabs-backup"></a>Sicherung über DPM/MABS

Die Sicherung über MABS oder DPM und Azure Backup wird wie folgt durchgeführt:

1. Auf allen Computern, die gesichert werden, wird jeweils der DPM- oder MABS-Schutz-Agent installiert.
1. Computer und Apps werden im lokalen Speicher in DPM oder MABS gesichert.
1. Der MARS-Agent (Microsoft Azure Recovery Services) wird auf dem DPM-Server oder auf MABS installiert.
1. Mit dem MARS-Agent werden die DPM- oder MABS-Datenträger mithilfe von Azure Backup in einem Recovery Services-Sicherungstresor in Azure gesichert.

Weitere Informationen finden Sie unter:

- Weitere Informationen zur [MABS-Architektur](backup-architecture.md#architecture-back-up-to-dpmmabs)
- [Unterstützungsmatrix](backup-support-matrix-mars-agent.md) für den MARS-Agent

## <a name="supported-scenarios"></a>Unterstützte Szenarios

**Szenario** | **Agent** | **Location**
--- | --- | ---
**Sichern lokaler Computer/Workloads** | Der DPM- oder MABS-Schutz-Agent wird auf den Computern ausgeführt, die Sie sichern möchten.<br/><br/> MARS-Agent auf dem MABS- oder DPM-Server<br/> Die zum Aktivieren dieses Features erforderliche Mindestversion des Microsoft Azure Recovery Services-Agents bzw. Azure Backup-Agents ist 2.0.8719.0.  | MABS/DPM muss lokal ausgeführt werden.

## <a name="supported-deployments"></a>Unterstützte Bereitstellungen

DPM und MABS können wie in der folgenden Tabelle zusammengefasst bereitgestellt werden.

**Bereitstellung** | **Unterstützung** | **Details**
--- | --- | ---
**Lokale Bereitstellung** | Physischer Server<br/><br/>Virtueller Hyper-V-Computer<br/><br/> Virtueller VMware-Computer | Weitere Informationen finden Sie in der [Schutzmatrix](backup-mabs-protection-matrix.md). 
**Bereitstellung als virtueller Azure Stack-Computer** | Nur MABS | DPM kann nicht zum Sichern virtueller Azure Stack-Computer verwendet werden.
**Bereitstellung als virtueller Azure-Computer** | Schutz von virtuellen Azure-Computern und Workloads, die auf diesen VMs ausgeführt werden | Bei Ausführung von MABS/DPM in Azure können lokale Computer nicht gesichert werden.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Unterstützte MABS- und DPM-Betriebssysteme

Mit Azure Backup können MABS- und DPM-Instanzen bei Ausführung unter den folgenden Betriebssystemen gesichert werden. Die Betriebssysteme müssen mit den neuesten Service Packs und Updates ausgeführt werden.

**Szenario** | **DPM/MABS**
--- | ---
**MABS auf einem virtuellen Azure-Computer** |  Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> Es empfiehlt sich, mit einem Image aus dem Marketplace zu beginnen.<br/><br/> Mindestens Standard_A4_v2 mit vier Kernen und 8 GB RAM
**DPM auf einem virtuellen Azure-Computer** | System Center 2012 R2 mit Update 3 oder höher<br/><br/> Windows-Betriebssystem entsprechend den [Anforderungen für System Center](/system-center/dpm/prepare-environment-for-dpm#dpm-server)<br/><br/> Es empfiehlt sich, mit einem Image aus dem Marketplace zu beginnen.<br/><br/> Mindestens Standard_A4_v2 mit vier Kernen und 8 GB RAM
**MABS auf einem lokalen Computer** |  MABS V3 und höher: Windows Server 2016 oder Windows Server 2019
**DPM auf einem lokalen Computer** | Physischer Server und virtueller Hyper-V-Computer: System Center 2012 SP1 oder höher<br/><br/> Virtueller VMware-Computer: System Center 2012 R2 mit Update 5 oder höher

>[!NOTE]
>Unter Windows Server Core oder Microsoft Hyper-V Server wird die Installation von Azure Backup Server nicht unterstützt.

## <a name="management-support"></a>Verwaltungsunterstützung

**Problem** | **Details**
--- | ---
**Installation** | Installieren Sie DPM oder MABS auf einem zweckgebundenen Computer.<br/><br/> Installieren Sie DPM oder MABS nicht auf einem Domänencontroller, auf einem Computer mit installierter Anwendungsserverrolle, auf einem Computer mit Exchange Server oder System Center Operations Manager oder auf einem Clusterknoten.<br/><br/> [Hier](/system-center/dpm/prepare-environment-for-dpm#dpm-server) finden Sie alle Anforderungen für das DPM-System.
**Domäne** | DPM- bzw. MABS-Instanzen müssen mit einer Domäne verknüpft werden. Führen Sie zunächst die Installation durch, und verknüpfen Sie DPM oder MABS dann mit einer Domäne. Nach der Bereitstellung kann DPM oder MABS nicht in eine neue Domäne verschoben werden.
**Storage** | Modern Backup Storage (MBS) wird ab DPM 2016 und MABS v2 und höher unterstützt. In MABS v1 ist die Funktion dagegen nicht verfügbar.
**Upgrade von MABS** | Sie können MABS v3 direkt installieren oder ein Upgrade von MABS v2 auf MABS v3 durchführen. [Weitere Informationen](backup-azure-microsoft-azure-backup.md#upgrade-mabs)
**Verschieben von MABS** | Bei Verwendung von MBS kann MABS unter Beibehaltung des Speichers auf einen neuen Server verschoben werden.<br/><br/> Der neue und der ursprüngliche Server müssen den gleichen Namen haben. Sie können den Namen nicht ändern, wenn der Speicherpool beibehalten werden soll und Sie dieselbe MABS-Datenbank zum Speichern von Datenwiederherstellungspunkten verwenden möchten.<br/><br/> Sie benötigen eine Sicherungskopie der MABS-Datenbank, da Sie sie wiederherstellen müssen.

## <a name="mabs-support-on-azure-stack"></a>Unterstützung von MABS in Azure Stack

Sie können MABS auf einem virtuellen Azure Stack-Computer bereitstellen, sodass Sie die Sicherung von virtuellen Azure Stack-Computern und deren Workloads an einem zentralen Ort verwalten können.

**Komponente** | **Details**
--- | ---
**MABS auf einem virtuellen Azure Stack-Computer** | Mindestens Größe A2. Es empfiehlt sich, mit einem Windows Server 2012 R2- oder Windows Server 2016-Image aus Azure Marketplace zu beginnen.<br/><br/> Installieren Sie keine weiteren Komponenten auf dem virtuellen MABS-Computer.
**MABS-Speicher** | Verwenden Sie ein separates Speicherkonto für den virtuellen MABS-Computer. Für den in MABS ausgeführten MARS-Agent ist temporärer Speicher für einen Cachespeicherort und zum Speichern der Datenwiederherstellung aus der Cloud erforderlich.
**MABS-Speicherpool** | Die Größe des MABS-Speicherpools richtet sich nach der Anzahl und Größe der an den virtuellen MABS-Computer angefügten Datenträger. Für jede Azure Stack-VM-Größe ist eine maximale Anzahl an Datenträgern festgelegt. Beispielsweise umfasst A2 vier Datenträger.
**MABS-Aufbewahrung** | Bewahren Sie keine gesicherte Daten mehr als fünf Tage auf den lokalen MABS-Datenträgern auf.
**Hochskalieren von MABS** | Zum Hochskalieren Ihrer Bereitstellung können Sie die Größe des virtuellen MABS-Computers erhöhen, Sie können beispielsweise von der Serie A zu Serie D wechseln.<br/><br/> Sie können ebenso sicherstellen, dass Sie Daten mit Sicherung regelmäßig auf Azure abladen. Sie können ggf. auch zusätzliche MABS-Server erstellen.
**.NET Framework in MABS** | Auf dem virtuellen MABS-Computer muss .NET Framework 3.3 SP1 oder höher installiert sein.
**MABS-Domäne** | Der virtuelle MABS-Computer muss mit einer Domäne verknüpft sein. Ein Domänenbenutzer mit Administratorrechten muss MABS auf dem virtuellen Computer installieren.
**Datensicherung auf virtuellem Azure Stack-Computer** | Sie können Dateien, Ordner und Anwendungen sichern.
**Unterstützte Sicherung** | Folgende Betriebssysteme werden für virtuelle Computer, die Sie sichern möchten, unterstützt:<br/><br/> Windows Server Semi-Annual Channel (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server-Unterstützung für virtuelle Azure Stack-Computer** | Sichern von SQL Server 2016; SQL Server 2014, SQL Server 2012, SQL Server 2012 SP1<br/><br/> Sichern und Wiederherstellen der Datenbank
**SharePoint-Unterstützung für virtuelle Azure Stack-Computer** | SharePoint 2016, SharePoint 2013, SharePoint 2010<br/><br/> Sichern und Wiederherstellen einer Farm, einer Datenbank, eines Front-Ends und eines Webservers
**Netzwerkanforderungen für gesicherte virtuelle Computer** | Alle virtuellen Computer in der Azure Stack-Workload müssen demselben virtuellen Netzwerk und demselben Abonnement angehören.

## <a name="dpmmabs-networking-support"></a>DPM- und MABS-Netzwerkunterstützung

### <a name="url-access"></a>URL-Zugriff

Der DPM-Server/MABS-Server benötigt Zugriff auf diese URLs und IP-Adressen:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-Adressen
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-Unterstützung

Sie können Ihre Daten über Azure ExpressRoute mit öffentlichem Peering (verfügbar für alte Verbindungen) und Microsoft-Peering sichern. Die Sicherung über privates Peering wird nicht unterstützt.

Bei öffentlichem Peering: Stellen Sie den Zugriff auf die folgenden Domänen/Adressen sicher:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-Adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

Mit Microsoft-Peering: Wählen Sie die folgenden Dienste, Regionen und relevanten Communitywerte aus:

- Azure Active Directory (12076:5060)
- Microsoft Azure-Region (entsprechend dem Standort Ihres Recovery Services-Tresors)
- Azure Storage (entsprechend dem Standort Ihres Recovery Services-Tresors)

Weitere Informationen finden Sie unter [ExpressRoute-Routinganforderungen](../expressroute/expressroute-routing.md).

>[!NOTE]
>Öffentliches Peering gilt für neue Leitungen als veraltet.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM- und MABS-Verbindung mit Azure Backup

Damit Sicherungen ordnungsgemäß durchgeführt werden, ist eine Verbindung mit dem Azure Backup-Dienst erforderlich, und das Azure-Abonnement muss aktiv sein. In der folgenden Tabelle ist das entsprechende Verhalten aufgeführt, wenn diese beiden Punkte nicht erfüllt sind.

**MABS und Azure** | **Abonnement** | **Sicherung/Wiederherstellung**
--- | --- | ---
Verbunden | Aktiv | Sicherung auf DPM- oder MABS-Datenträgern<br/><br/> Sicherung in Azure<br/><br/> Wiederherstellung von einem Datenträger<br/><br/> Wiederherstellung von Azure
Verbunden | Abgelaufen oder Bereitstellung aufgehoben | Keine Sicherung auf Datenträger oder in Azure<br/><br/> Wenn das Abonnement abgelaufen ist, können Sie die Wiederherstellung von einem Datenträger oder Azure durchführen.<br/><br/> Wenn das Abonnement außer Betrieb gesetzt ist, können Sie keine Wiederherstellung von einem Datenträger oder Azure durchführen. Die Azure-Wiederherstellungspunkte werden gelöscht.
Keine Verbindung für mehr als 15 Tage | Aktiv | Keine Sicherung auf Datenträger oder in Azure<br/><br/> Sie können die Wiederherstellung von einem Datenträger oder Azure durchführen.
Keine Verbindung für mehr als 15 Tage | Abgelaufen oder Bereitstellung aufgehoben | Keine Sicherung auf Datenträger oder in Azure<br/><br/> Wenn das Abonnement abgelaufen ist, können Sie die Wiederherstellung von einem Datenträger oder Azure durchführen.<br/><br/> Wenn das Abonnement außer Betrieb gesetzt ist, können Sie keine Wiederherstellung von einem Datenträger oder Azure durchführen. Die Azure-Wiederherstellungspunkte werden gelöscht.

## <a name="domain-and-domain-trusts-support"></a>Unterstützung von Domänen und Domänenvertrauensstellungen

|Anforderung |Details |
|---------|---------|
|Domain    | Der DPM/MABS-Server muss sich in einer Windows Server 2019-, Windows Server 2016-, Windows Server 2012 R2- oder Windows Server 2012-Domäne befinden.        |
|Domänenvertrauensstellung   |  DPM/MABS unterstützt Datenschutz über Gesamtstrukturen hinweg, sofern Sie auf Gesamtstrukturebene eine bidirektionale Vertrauensstellung zwischen den einzelnen Gesamtstrukturen einrichten.   <BR><BR>   Server und Arbeitsstationen können in einer Gesamtstruktur, die eine bidirektionale Vertrauensstellung mit der DPM/MABS-Serverdomäne hat, von DPM/MABS domänenübergreifend geschützt werden. Informationen zum Schützen von Computern in Arbeitsgruppen oder nicht vertrauenswürdigen Domänen finden Sie unter [Sichern und Wiederherstellen von Workloads in Arbeitsgruppen und nicht vertrauenswürdigen Domänen](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains).  |

## <a name="dpmmabs-storage-support"></a>DPM- oder MABS-Speicherunterstützung

Die in DPM oder MABS gesicherten Daten werden im lokalen Datenträgerspeicher gespeichert.

**Storage** | **Details**
--- | ---
**MBS** | Modern Backup Storage (MBS) wird ab DPM 2016 und MABS v2 und höher unterstützt. In MABS v1 ist die Funktion dagegen nicht verfügbar.
**MABS-Speicher auf virtuellem Azure-Computer** | Daten werden auf Azure-Datenträgern gespeichert, die der DPM/MABS-VM zugeordnet sind und die in DPM/MABS verwaltet werden. Die Anzahl der Datenträger, die für DPM/MABS-Speicherpools verwendet werden kann, ist durch die Größe der VM beschränkt.<br/><br/> VM A2: 4 Datenträger, VM A3: 8 Datenträger, VM A4: 16 Datenträger mit einer maximalen Größe von 1 TB für jeden Datenträger. Dies bestimmt den gesamten verfügbaren Sicherungsspeicherpool.<br/><br/> Die Menge der Daten, die gesichert werden können, hängt von der Anzahl und Größe der angefügten Datenträger ab.
**MABS-Datenaufbewahrung auf virtuellem Azure-Computer** | Es wird empfohlen, Daten einen Tag lang in DPM oder MABS auf dem Azure-Datenträger aufzubewahren und zur längeren Aufbewahrung eine Sicherung von DPM oder MABS in den Tresor durchzuführen. So können Sie durch Auslagerung in Azure Backup eine größere Menge an Daten schützen.

### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)

Ab DPM 2016 und MABS v2 (unter Windows Server 2016) und höher können Sie Modern Backup Storage (MBS) nutzen.

- MBS-Sicherungen werden auf einem ReFS-Datenträger (Resilient File System) gespeichert.
- In MBS wird das Klonen von ReFS-Blöcken zur schnelleren Sicherung und effizienteren Verwendung von Speicherplatz verwendet.
- Wenn Sie dem lokalen DPM- oder MABS-Speicherpool Volumes hinzufügen, konfigurieren Sie sie mit Laufwerkbuchstaben. Dann können Sie den Workloadspeicher auf verschiedenen Volumes konfigurieren.
- Bei der Erstellung von Schutzgruppen zum Sichern von Daten in DPM oder MABS wählen Sie das gewünschte Laufwerk aus. Beispielsweise können Sie Sicherungen für SQL- oder andere Workloads mit hohem IOPS-Wert auf einem Laufwerk mit hoher Leistung speichern. Sie können auch Workloads auf einem Laufwerk mit geringerer Leistung speichern, die weniger häufig gesichert werden.

## <a name="supported-backups-to-mabs"></a>Unterstützte Sicherungen in MABS

Informationen zu den verschiedenen Servern und Workloads, die Sie mit Azure Backup Server schützen können, finden Sie in der [Schutzunterstützungsmatrix für Azure Backup](./backup-mabs-protection-matrix.md#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Unterstützte Sicherungen in DPM

Informationen zu den verschiedenen Servern und Workloads, die Sie mit Data Protection Manager schützen können, finden Sie im Artikel [Was kann mit DPM gesichert werden?](/system-center/dpm/dpm-protection-matrix).

- Clusterworkloads, die mit DPM oder MABS gesichert werden, müssen sich in derselben Domäne wie DPM bzw. MABS oder in einer untergeordneten oder vertrauenswürdigen Domäne befinden.
- Sie können die NTLM- oder Zertifikatauthentifizierung verwenden, um Daten in nicht vertrauenswürdigen Domänen oder Arbeitsgruppen zu sichern.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [MABS-Architektur](backup-architecture.md#architecture-back-up-to-dpmmabs)
- [Unterstützungsmatrix](backup-support-matrix-mars-agent.md) für den MARS-Agent
- [Einrichten](backup-azure-microsoft-azure-backup.md) eines MABS-Servers
- [DPM einrichten](/system-center/dpm/install-dpm)
