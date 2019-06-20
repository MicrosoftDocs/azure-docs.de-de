---
title: Unterstützungsmatrix für Microsoft Azure Backup Server und System Center DPM
description: Dieser Artikel enthält eine Zusammenfassung der Unterstützung für Azure Backup bei Verwendung von Microsoft Azure Backup Server oder System Center DPM zum Sichern von lokalen Ressourcen und Ressourcen auf virtuellen Azure-Computern.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 704bb409d2b21e2ae258dbb2d627b1c088d80db7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254649"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Unterstützungsmatrix für die Sicherung mit Microsoft Azure Backup Server oder System Center DPM

Mit dem [Azure Backup-Dienst](backup-overview.md) können Sie lokale Computer und Workloads sowie virtuelle Azure-Computer (VMs) sichern. In diesem Artikel werden die Unterstützungseinstellungen und Einschränkungen für die Sicherung von Computern mithilfe von Microsoft Azure Backup Server (MABS) oder System Center Data Protection Manager (DPM) und Azure Backup zusammenfassend dargestellt.

## <a name="about-dpmmabs"></a>Informationen zu DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) ist eine Unternehmenslösung, mit der die Sicherung und Wiederherstellung von Computern und Daten für Unternehmen konfiguriert, erleichtert und verwaltet werden. Diese Lösung ist Teil der [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing)-Produktsuite.

MABS ist ein Serverprodukt, mit dem lokale physische Server, VMs und die darauf ausgeführten Anwendungen gesichert werden können.

MABS basiert auf System Center DPM und umfasst ähnliche Funktionen mit einigen Unterschieden:
- Für die Ausführung von MABS ist keine Lizenz für System Center erforderlich.
- In Azure ist für MABS und für DPM die langfristige Sicherungsspeicherung möglich. Darüber hinaus können Sie mit DPM Daten zur langfristigen Speicherung auf Band sichern. Diese Funktion ist in MABS nicht enthalten.

Sie können MABS über das [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520) herunterladen. Es kann lokal oder auf einem virtuellen Azure-Computer ausgeführt werden.

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

## <a name="supported-scenarios"></a>Unterstützte Szenarien 

**Szenario** | **Agent** | **Location**
--- | --- | ---
**Sichern lokaler Computer/Workloads** | Der DPM- oder MABS-Schutz-Agent wird auf den Computern ausgeführt, die Sie sichern möchten.<br/><br/> MARS-Agent auf dem MABS- oder DPM-Server | MABS/DPM muss lokal ausgeführt werden.
**Sichern von virtuellen Azure-Computern/Azure-Workloads** | DPM- oder MABS-Schutz-Agent auf geschütztem Computer<br/><br/> MARS-Agent auf dem MABS- oder DPM-Server | DPM/MABS muss auf einem virtuellen Azure-Computer ausgeführt werden.

## <a name="supported-deployments"></a>Unterstützte Bereitstellungen

DPM und MABS können wie in der folgenden Tabelle zusammengefasst bereitgestellt werden.

**Bereitstellung** | **Unterstützung** | **Details**
--- | --- | ---
**Lokale Bereitstellung** | Physischer Server<br/><br/>Virtueller Hyper-V-Computer<br/><br/> Virtueller VMware-Computer | Wenn DPM/MABS als virtueller VMware-Computer installiert ist, werden nur virtuelle VMware-Computer und darauf ausgeführte Workloads gesichert.
**Bereitstellung als virtueller Azure Stack-Computer** | Nur MABS | DPM kann nicht zum Sichern virtueller Azure Stack-Computer verwendet werden.
**Bereitstellung als virtueller Azure-Computer** | Schutz von virtuellen Azure-Computern und Workloads, die auf diesen VMs ausgeführt werden | Bei Ausführung von MABS/DPM in Azure können lokale Computer nicht gesichert werden.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Unterstützte MABS- und DPM-Betriebssysteme

Mit Azure Backup können MABS- und DPM-Instanzen bei Ausführung unter den folgenden Betriebssystemen gesichert werden. Die Betriebssysteme müssen mit den neuesten Service Packs und Updates ausgeführt werden.

**Szenario** | **DPM/MABS** 
--- | --- 
**MABS auf einem virtuellen Azure-Computer** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> Es empfiehlt sich, mit einem Image aus dem Marketplace zu beginnen.<br/><br/> Mindestens A2-Standard mit zwei Kernen und 3,5 GB RAM 
**DPM auf einem virtuellen Azure-Computer** | System Center 2012 R2 mit Update 3 oder höher<br/><br/> Windows-Betriebssystem entsprechend den [Anforderungen für System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)<br/><br/> Es empfiehlt sich, mit einem Image aus dem Marketplace zu beginnen.<br/><br/> Mindestens A2-Standard mit zwei Kernen und 3,5 GB RAM 
**MABS auf einem lokalen Computer** | Unterstützte 64-Bit-Betriebssysteme<br/><br/> MABS V3 und höher: Windows Server 2019 (Standard, Datacenter, Essentials) <br/><br/> MABS V2 und höher: Windows Server 2016 (Standard, Datacenter, Essentials)<br/><br/> Alle MABS-Versionen: Windows Server 2012 R2, Windows Server 2012 (Standard, Datacenter, Foundation)<br/><br/>Alle MABS-Versionen: Windows Storage Server 2012 R2, Windows Server 2012 (Standard, Workgroup)
**DPM auf einem lokalen Computer** | Physischer Server und virtueller Hyper-V-Computer: System Center 2012 SP1 oder höher<br/><br/> Virtueller VMware-Computer: System Center 2012 R2 mit Update 5 oder höher 



## <a name="management-support"></a>Verwaltungsunterstützung

**Problem** | **Details**
--- | ---
**Installation** | Installieren Sie DPM oder MABS auf einem zweckgebundenen Computer.<br/><br/> Installieren Sie DPM oder MABS nicht auf einem Domänencontroller, auf einem Computer mit installierter Anwendungsserverrolle, auf einem Computer mit Exchange Server oder System Center Operations Manager oder auf einem Clusterknoten.<br/><br/> [Hier](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) finden Sie alle Anforderungen für das DPM-System.
**Domäne** | DPM- bzw. MABS-Instanzen müssen mit einer Domäne verknüpft werden. Führen Sie zunächst die Installation durch, und verknüpfen Sie DPM oder MABS dann mit einer Domäne. Nach der Bereitstellung kann DPM oder MABS nicht in eine neue Domäne verschoben werden.
**Speicher** | Modern Backup Storage (MBS) wird ab DPM 2016 und MABS v2 und höher unterstützt. In MABS v1 ist die Funktion dagegen nicht verfügbar.
**Upgrade von MABS** | Sie können MABS v3 direkt installieren oder ein Upgrade von MABS v2 auf MABS v3 durchführen. [Weitere Informationen](backup-azure-microsoft-azure-backup.md#upgrade-mabs)
**Verschieben von MABS** | Bei Verwendung von MBS kann MABS unter Beibehaltung des Speichers auf einen neuen Server verschoben werden.<br/><br/> Der neue und der ursprüngliche Server müssen den gleichen Namen haben. Sie können den Namen nicht ändern, wenn der Speicherpool beibehalten werden soll und Sie dieselbe MABS-Datenbank zum Speichern von Datenwiederherstellungspunkten verwenden möchten.<br/><br/> Sie benötigen eine Sicherungskopie der MABS-Datenbank, da Sie sie wiederherstellen müssen.


## <a name="mabs-support-on-azure-stack"></a>Unterstützung von MABS in Azure Stack

Sie können MABS auf einem virtuellen Azure Stack-Computer bereitstellen, sodass Sie die Sicherung von virtuellen Azure Stack-Computern und deren Workloads an einem zentralen Ort verwalten können.

**Komponente** | **Details**
--- | --- 
**MABS auf einem virtuellen Azure Stack-Computer** | Mindestens Größe A2. Es empfiehlt sich, mit einem Windows Server 2012 R2- oder Windows Server 2016-Image aus dem Azure Marketplace zu beginnen.<br/><br/> Installieren Sie keine weiteren Komponenten auf dem virtuellen MABS-Computer.
**MABS-Speicher** | Verwenden Sie ein separates Speicherkonto für den virtuellen MABS-Computer. Für den in MABS ausgeführten MARS-Agent ist temporärer Speicher für einen Cachespeicherort und zum Speichern der Datenwiederherstellung aus der Cloud erforderlich.
**MABS-Speicherpool** | Die Größe des MABS-Speicherpools richtet sich nach der Anzahl und Größe der an den virtuellen MABS-Computer angefügten Datenträger. Für jede Azure Stack-VM-Größe ist eine maximale Anzahl an Datenträgern festgelegt. Beispielsweise umfasst A2 vier Datenträger.
**MABS-Aufbewahrung** | Bewahren Sie keine gesicherte Daten mehr als fünf Tage auf den lokalen MABS-Datenträgern auf.
**Zentrales Hochskalieren von MABS** | Zum zentralen Hochskalieren Ihrer Bereitstellung können Sie die Größe des virtuellen MABS-Computers erhöhen, Sie können beispielsweise von der Serie A zu Serie D wechseln.<br/><br/> Sie können ebenso sicherstellen, dass Sie Daten mit Sicherung regelmäßig auf Azure abladen. Sie können ggf. auch zusätzliche MABS-Server erstellen.
**.NET Framework in MABS** | Auf dem virtuellen MABS-Computer muss .NET Framework 3.3 SP1 oder höher installiert sein.
**MABS-Domäne** | Der virtuelle MABS-Computer muss mit einer Domäne verknüpft sein. Ein Domänenbenutzer mit Administratorrechten muss MABS auf dem virtuellen Computer installieren.
**Datensicherung auf virtuellem Azure Stack-Computer** | Sie können Dateien, Ordner und Anwendungen sichern.
**Unterstützte Sicherung** | Folgende Betriebssysteme werden für virtuelle Computer, die Sie sichern möchten, unterstützt:<br/><br/> Windows Server Semi-Annual Channel (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2
**SQL Server-Unterstützung für virtuelle Azure Stack-Computer** | Sichern von SQL Server 2016; SQL Server 2014, SQL Server 2012, SQL Server 2012 SP1<br/><br/> Sichern und Wiederherstellen der Datenbank
**SharePoint-Unterstützung für virtuelle Azure Stack-Computer** | SharePoint 2016, SharePoint 2013, SharePoint 2010<br/><br/> Sichern und Wiederherstellen einer Farm, einer Datenbank, eines Front-Ends und eines Webservers
**Netzwerkanforderungen für gesicherte virtuelle Computer** | Alle virtuellen Computer in der Azure Stack-Workload müssen demselben virtuellen Netzwerk und demselben Abonnement angehören.

## <a name="dpmmabs-networking-support"></a>DPM- und MABS-Netzwerkunterstützung

### <a name="url-access"></a>URL-Zugriff

Der DPM-Server oder MABS benötigt Zugriff auf diese URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM- und MABS-Verbindung mit Azure Backup

Damit Sicherungen ordnungsgemäß durchgeführt werden, ist eine Verbindung mit dem Azure Backup-Dienst erforderlich, und das Azure-Abonnement muss aktiv sein. In der folgenden Tabelle ist das entsprechende Verhalten aufgeführt, wenn diese beiden Punkte nicht erfüllt sind.

**MABS und Azure** | **Abonnement** | **Sicherung/Wiederherstellung** 
--- | --- | --- 
Verbunden | Aktiv | Sicherung auf DPM- oder MABS-Datenträgern<br/><br/> Sicherung in Azure<br/><br/> Wiederherstellung von einem Datenträger<br/><br/> Wiederherstellung von Azure
Verbunden | Abgelaufen oder Bereitstellung aufgehoben | Keine Sicherung auf Datenträger oder in Azure<br/><br/> Wenn das Abonnement abgelaufen ist, können Sie die Wiederherstellung von einem Datenträger oder Azure durchführen.<br/><br/> Wenn das Abonnement außer Betrieb gesetzt ist, können Sie keine Wiederherstellung von einem Datenträger oder Azure durchführen. Die Azure-Wiederherstellungspunkte werden gelöscht.
Keine Verbindung für mehr als 15 Tage | Aktiv | Keine Sicherung auf Datenträger oder in Azure<br/><br/> Sie können die Wiederherstellung von einem Datenträger oder Azure durchführen.
Keine Verbindung für mehr als 15 Tage | Abgelaufen oder Bereitstellung aufgehoben | Keine Sicherung auf Datenträger oder in Azure<br/><br/> Wenn das Abonnement abgelaufen ist, können Sie die Wiederherstellung von einem Datenträger oder Azure durchführen.<br/><br/> Wenn das Abonnement außer Betrieb gesetzt ist, können Sie keine Wiederherstellung von einem Datenträger oder Azure durchführen. Die Azure-Wiederherstellungspunkte werden gelöscht.

## <a name="dpmmabs-storage-support"></a>DPM- oder MABS-Speicherunterstützung

Die in DPM oder MABS gesicherten Daten werden im lokalen Datenträgerspeicher gespeichert. 

**Speicher** | **Details**
--- | ---
**MBS** | Modern Backup Storage (MBS) wird ab DPM 2016 und MABS v2 und höher unterstützt. In MABS v1 ist die Funktion dagegen nicht verfügbar.
**MABS-Speicher auf virtuellem Azure-Computer** | Daten werden auf Azure-Datenträgern gespeichert, die der DPM/MABS-VM zugeordnet sind und die in DPM/MABS verwaltet werden. Die Anzahl der Datenträger, die für DPM/MABS-Speicherpools verwendet werden kann, ist durch die Größe der VM beschränkt.<br/><br/> VM A2: 4 Datenträger, VM A3: 8 Datenträger, VM A4: 16 Datenträger mit einer maximalen Größe von 1 TB für jeden Datenträger. Dies bestimmt den gesamten verfügbaren Sicherungsspeicherpool.<br/><br/> Die Menge der Daten, die gesichert werden können, hängt von der Anzahl und Größe der angefügten Datenträger ab.
**MABS-Datenaufbewahrung auf virtuellem Azure-Computer** | Es wird empfohlen, Daten einen Tag lang in DPM oder MABS auf dem Azure-Datenträger aufzubewahren und zur längeren Aufbewahrung eine Sicherung von DPM oder MABS in den Tresor durchzuführen. Durch Auslagerung in Azure Backup können Sie so eine größere Menge an Daten schützen.


### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)
Ab DPM 2016 und MABS v2 (unter Windows Server 2016) und höher können Sie Modern Backup Storage (MBS) nutzen.

- MBS-Sicherungen werden auf einem ReFS-Datenträger (Resilient File System) gespeichert.
- In MBS wird das Klonen von ReFS-Blöcken zur schnelleren Sicherung und effizienteren Verwendung von Speicherplatz verwendet.
- Wenn Sie dem lokalen DPM- oder MABS-Speicherpool Volumes hinzufügen, konfigurieren Sie sie mit Laufwerkbuchstaben. Dann können Sie den Workloadspeicher auf verschiedenen Volumes konfigurieren.
- Bei der Erstellung von Schutzgruppen zum Sichern von Daten in DPM oder MABS wählen Sie das gewünschte Laufwerk aus. Beispielsweise können Sie Sicherungen für SQL- oder andere Workloads mit hohem IOPS-Wert auf einem Laufwerk mit hoher Leistung speichern. Sie können auch Workloads auf einem Laufwerk mit geringerer Leistung speichern, die weniger häufig gesichert werden.


## <a name="supported-backups-to-mabs"></a>Unterstützte Sicherungen in MABS

In der folgenden Tabelle ist zusammenfassend angegeben, welche Elemente von lokalen Computern und virtuellen Azure-Computern in MABS gesichert werden können.


**Sicherung** | **Versionen** | **MABS** | **Details** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 Bit) | MABS v3, v2, v1 | Lokal | Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden unterstützt.<br/><br/> Volumes müssen mindestens 1 GB groß und mit NTFS formatiert sein. |
**Windows Server 2016 (Datacenter, Standard, nicht Nano)**<br/><br/> 64/32 Bit | MABS v3, v2 | Lokal und virtueller Azure-Computer| Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> Deduplizierte Volumes werden unterstützt. |
**Windows Server 2012 R2 (Datacenter und Standard)**<br/><br/> 64/32 Bit | MABS v3, v2, v1 | Lokal und virtueller Azure-Computer | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden unterstützt. |
**Windows Server 2012 mit SP1 (Datacenter und Standard)**<br/><br/> 64/32 Bit | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) muss installiert sein. | Lokal und virtueller Azure-Computer | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden unterstützt. |
**Windows Server 2012 (Datacenter und Standard)**<br/><br/> 64/32 Bit | MABS v1 | Lokal und virtueller Azure-Computer | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden unterstützt. |
**Windows 2008 R2 mit SP1 (Standard und Enterprise)**<br/><br/> 64/32 Bit | Wird in MABS v3, v2 und v1 unterstützt.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) muss installiert sein. | Lokal und virtueller Azure-Computer |   **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden unterstützt. |
**Windows 2008 R2 (Standard und Enterprise)**<br/><br/> 64/32 Bit | MABS v1. Für MABS v2 und v3 muss das Betriebssystem mit SP1 ausgeführt werden. | Lokal und virtueller Azure-Computer | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden unterstützt. |
**Windows Server 2008 mit SP2**<br/><br/> 64/32 Bit | MABS v1, v2, v3 | Wenn MABS als lokaler physischer Computer oder virtueller Hyper-V-Computer bereitgestellt wird, wird nur v1 unterstützt.<br/><br/> Wenn MABS als virtueller VMware-Computer bereitgestellt wird, werden MABS v1, v2 und v3 unterstützt.<br/><br/> Wird für MABS auf einem virtuellen Azure-Computer nicht unterstützt. | Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal |
**Windows Storage Server 2008** | MABS v1, v2, v3 | MABS als lokaler physischer Server oder virtueller Hyper-V-Computer <br/><br/> Wird für MABS auf einem virtuellen Azure-Computer nicht unterstützt. | Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal
**SQL Server 2017** | MABS v3 | Lokal und virtueller Azure-Computer| Sichern einer SQL Server-Datenbank<br/><br/> Sicherung eines SQL Server-Clusters wird unterstützt.<br/><br/>In freigegebenen Clustervolumes gespeicherte Datenbanken werden nicht unterstützt. |
**SQL Server 2016, 2016 mit SP1** | MABS v3, v2 | Lokal und virtueller Azure-Computer| Sichern einer SQL Server-Datenbank<br/><br/> Sicherung eines SQL Server-Clusters wird unterstützt.<br/><br/>In freigegebenen Clustervolumes gespeicherte Datenbanken werden nicht unterstützt. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | Lokal und virtueller Azure-Computer| Sichern einer SQL Server-Datenbank<br/><br/> Sicherung eines SQL Server-Clusters wird unterstützt.<br/><br/>In freigegebenen Clustervolumes gespeicherte Datenbanken werden nicht unterstützt. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, v1 | Lokal | Sichern eines eigenständigen Exchange-Servers, einer Datenbank unter einer Database Availability Group (DAG)<br/><br/> Wiederherstellung eines Postfachs, einer Postfachdatenbank unter einer DAG<br/><br/> ReFS wird nicht unterstützt.<br/><br/> Sichern nicht freigegebener Datenträgercluster<br/><br/> Sichern von Exchange Server mit Konfiguration für fortlaufende Replikation |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | Lokal und virtueller Azure-Computer | Sichern einer Farm und eines Front-End-Webservers<br/><br/> Wiederherstellen einer Farm, einer Datenbank, einer Webanwendung, einer Datei oder eines Listenelements, einer SharePoint-Suche und eines Front-End-Webservers<br/><br/> Eine Farm mit SQL Server-Always On für die Inhaltsdatenbanken kann nicht gesichert werden. |
**Hyper-V unter Windows Server 2016**<br/><br/> **Windows Server 2012 R2, 2012** (Datacenter, Standard)<br/><br/> **Windows Server 2008 R2 (mit SP1)** | MABS v3, v2, v1 | Lokal | **MABS-Agent auf Hyper-V-Host:** Sichern der gesamten virtuellen Computer und Hostdatendateien Sichern von virtuellen Computern mit lokalem Speicher, von virtuellen Computern in Clustern mit Speicher für freigegebene Clustervolumes und von virtuellen Computern mit SMB-Dateispeicherspeicher<br/><br/> **MABS-Agent auf virtuellem Gastcomputer:** Sichern von auf dem virtuellen Computer ausgeführten Workloads Freigegebene Clustervolumes<br/><br/> **Wiederherstellung:** Virtueller Computer, Wiederherstellung auf Elementebene von VHDs, Volumes, Ordnern und Dateien<br/><br/> **Virtuelle Linux-Computer:** Sicherung, wenn Hyper-V unter Windows Server 2012 R2 und höher ausgeführt wird. Wiederherstellung für virtuelle Linux-Computer betrifft den gesamten Computer. |
**Virtuelle VMware-Computer: vCenter/vSphere ESXi 5.5, 6.0, 6.5** | MABS v3, v2, v1<br/><br/> MABS v1 erfordert Updaterollup 1. | Lokal | Sichern von virtuellen VMware-Computern in freigegebenen Clustervolumes, in NFS- und SAN-Speicher<br/><br/> Wiederherstellen des gesamten virtuellen Computers<br/><br/> Windows- und Linux-Sicherung<br/><br/> Wiederherstellung auf Elementebene von Ordnern und Dateien nur für virtuelle Windows-Computer<br/><br/> VMware vApp wird nicht unterstützt.<br/><br/> Wiederherstellung für virtuelle Linux-Computer betrifft den gesamten Computer. | 



## <a name="supported-backups-to-dpm"></a>Unterstützte Sicherungen in DPM

In der folgenden Tabelle ist zusammenfassend angegeben, welche Elemente von lokalen Computern und virtuellen Azure-Computern in DPM gesichert werden können.



**Sicherung** | **DPM** | **Details**
--- | --- | --- 
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 Bit) | Nur lokal<br/><br/> Für Windows 10 mit DPM 2012 R2 wird die Installation von[Update 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) empfohlen. | Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden unterstützt.<br/><br/> Volumes müssen mindestens 1 GB groß und mit NTFS formatiert sein.
**Windows Server 2016 (Datacenter, Standard, nicht Nano)**<br/><br/> 64/32 Bit | Lokal und virtueller Azure-Computer<br/><br/> Nur DPM 2016| Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> Deduplizierte Volumes werden unterstützt.
**Windows Server 2012 R2 (Datacenter und Standard)**<br/><br/> 64/32 Bit | Lokal und virtueller Azure-Computer | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden ab DPM 2012 R2 unterstützt.
**Windows Server 2012 mit SP1 (Datacenter und Standard)**<br/><br/> 64/32 Bit | Lokal und virtueller Azure-Computer | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden ab DPM 2012 R2 unterstützt.
**Windows Server 2012 (Datacenter und Standard)**<br/><br/> 64/32 Bit |  Lokal und virtueller Azure-Computer | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei<br/><br/> Deduplizierte Volumes werden ab DPM 2012 R2 unterstützt.
**Windows 2008 R2 mit SP1 (Standard und Enterprise)**<br/><br/> 64/32 Bit | Lokal und virtueller Azure-Computer<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) muss installiert sein. |   **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal<br/><br/> **Schutz auf virtuellem Azure-Computer:** Volume, Freigabe, Ordner, Datei
**Windows 2008 R2 (Standard und Enterprise)**<br/><br/> 64/32 Bit | Lokal<br/><br/> DPM kann nicht als virtueller VMware-Computer installiert werden.<br/><br/> DPM auf einem virtuellen Azure-Computer wird nicht unterstützt. | **Lokaler Schutz:** Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal
**Windows Server 2008 mit SP2**<br/><br/> 64/32 Bit | Nur lokal<br/><br/> DPM wird bei Ausführung als virtueller VMware-Computer unterstützt. Die Ausführung als physischer Server oder virtueller Hyper-V-Computer wird nicht unterstützt. | Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal
**Windows Storage Server 2008** | DPM bei lokaler Ausführung als physischer Server oder virtueller Hyper-V-Computer | Volume, Freigabe, Ordner, Datei; Systemstatus/Bare-Metal
**SQL Server 2017** | DPM SAC, DPM 2016 mit Updaterollup 5 oder höher<br/><br/> Lokal und virtueller Azure-Computer| Sichern einer SQL Server-Datenbank<br/><br/> Sicherung eines SQL Server-Clusters wird unterstützt.<br/><br/>In freigegebenen Clustervolumes gespeicherte Datenbanken werden nicht unterstützt.
**SQL Server 2016 mit SP1** | Wird für DPM 2012 R2 nicht unterstützt. Wird für DPM SAC und DPM 2016 mit Updaterollup 4 oder höher unterstützt.<br/><br/> Lokal und virtueller Azure-Computer| Sichern einer SQL Server-Datenbank<br/><br/> Sicherung eines SQL Server-Clusters wird unterstützt.<br/><br/>In freigegebenen Clustervolumes gespeicherte Datenbanken werden nicht unterstützt.
**SQL Server 2016** | Wird für DPM 2012 R2 nicht unterstützt. Wird für DPM SAC und DPM 2016 ab Updaterollup 2 unterstützt.<br/><br/> Lokal und virtueller Azure-Computer| Sichern einer SQL Server-Datenbank<br/><br/> Sicherung eines SQL Server-Clusters wird unterstützt.<br/><br/>In freigegebenen Clustervolumes gespeicherte Datenbanken werden nicht unterstützt.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 mit DPM 2012 R2 mit Updaterollup 4 und höher<br/><br/> Lokal und virtueller Azure-Computer| Sichern einer SQL Server-Datenbank<br/><br/> Sicherung eines SQL Server-Clusters wird unterstützt.<br/><br/>In freigegebenen Clustervolumes gespeicherte Datenbanken werden nicht unterstützt.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Für Exchange 2016 erfordert DPM 2012 R2 Updaterollup 9 oder höher.<br/><br/> Lokal | Sichern eines eigenständigen Exchange-Servers, einer Datenbank unter einer Database Availability Group (DAG)<br/><br/> Wiederherstellung eines Postfachs, einer Postfachdatenbank unter einer DAG<br/><br/> ReFS wird nicht unterstützt.<br/><br/> Sichern nicht freigegebener Datenträgercluster<br/><br/> Sichern von Exchange Server mit Konfiguration für fortlaufende Replikation
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 in DPM 2016 oder höher<br/><br/>Lokal und virtueller Azure-Computer | Sichern einer Farm und eines Front-End-Webservers<br/><br/> Wiederherstellen einer Farm, einer Datenbank, einer Webanwendung, einer Datei oder eines Listenelements, einer SharePoint-Suche und eines Front-End-Webservers<br/><br/> Eine Farm mit SQL Server-Always On für die Inhaltsdatenbanken kann nicht gesichert werden.
**Hyper-V unter Windows Server 2016**<br/><br/> **Windows Server 2012 R2, 2012** (Datacenter, Standard)<br/><br/> **Windows Server 2008 R2 (mit SP1)** | Hyper-V wird für DPM 2016 und höher unterstützt.<br/><br/> Lokal | **MABS-Agent auf Hyper-V-Host:** Sichern der gesamten virtuellen Computer und Hostdatendateien Sichern von virtuellen Computern mit lokalem Speicher, von virtuellen Computern in Clustern mit Speicher für freigegebene Clustervolumes und von virtuellen Computern mit SMB-Dateispeicherspeicher<br/><br/> **MABS-Agent auf virtuellem Gastcomputer:** Sichern von auf dem virtuellen Computer ausgeführten Workloads Freigegebene Clustervolumes<br/><br/> **Wiederherstellung:** Virtueller Computer, Wiederherstellung auf Elementebene von VHDs, Volumes, Ordnern und Dateien<br/><br/> **Virtuelle Linux-Computer:** Sicherung, wenn Hyper-V unter Windows Server 2012 R2 und höher ausgeführt wird. Wiederherstellung für virtuelle Linux-Computer betrifft den gesamten Computer.
**Virtuelle VMware-Computer: vCenter/vSphere ESXi 5.5, 6.0, 6.5** | MABS v3, v2, v1<br/><br/> DPM 2012 R2 erfordert System Center Updaterollup 1. <br/><br/>Lokal | Sichern von virtuellen VMware-Computern in freigegebenen Clustervolumes, in NFS- und SAN-Speicher<br/><br/> Wiederherstellen des gesamten virtuellen Computers<br/><br/> Windows- und Linux-Sicherung<br/><br/> Wiederherstellung auf Elementebene von Ordnern und Dateien nur für virtuelle Windows-Computer<br/><br/> VMware vApp wird nicht unterstützt.<br/><br/> Wiederherstellung für virtuelle Linux-Computer betrifft den gesamten Computer.


- Beachten Sie, dass sich Clusterworkloads, die mit DPM oder MABS gesichert werden, in derselben Domäne wie DPM bzw. MABS oder in einer untergeordneten oder vertrauenswürdigen Domäne befinden müssen. 
- Sie können die NTLM- oder Zertifikatauthentifizierung verwenden, um Daten in nicht vertrauenswürdigen Domänen oder Arbeitsgruppen zu sichern.



## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [MABS-Architektur](backup-architecture.md#architecture-back-up-to-dpmmabs)
- [Unterstützungsmatrix](backup-support-matrix-mars-agent.md) für den MARS-Agent
- [Einrichten](backup-azure-microsoft-azure-backup.md) eines MABS-Servers
- [DPM einrichten](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)
