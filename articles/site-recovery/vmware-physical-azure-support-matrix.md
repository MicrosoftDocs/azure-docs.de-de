---
title: Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die unterstützten Betriebssysteme und Komponenten für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure mit Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: raynew
ms.openlocfilehash: 514aaaf7a274e60a17bbae62b3c62e7cf3668e7a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237300"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure

Dieser Artikel enthält eine Übersicht über die unterstützten Komponenten und Einstellungen für die Notfallwiederherstellung von VMware-VMs in Azure mit [Azure Site Recovery](site-recovery-overview.md).

Sehen Sie sich unsere [Tutorials](tutorial-prepare-azure.md) an, um mit dem einfachsten Bereitstellungsszenario in Azure Site Recovery einzusteigen. [Hier](vmware-azure-architecture.md) finden Sie weitere Informationen zur Azure Site Recovery-Architektur.

## <a name="deployment-scenario"></a>Bereitstellungsszenario

**Szenario** | **Details**
--- | ---
Notfallwiederherstellung von virtuellen VMware-Computern | Replikation lokaler VMware-VMs in Azure. Dieses Szenario können Sie über das Azure-Portal oder mit [PowerShell](vmware-azure-disaster-recovery-powershell.md) bereitstellen.
Notfallwiederherstellung von physischen Servern | Die Replikation lokaler physischer Windows-/Linux-Server in Azure. Dieses Szenario können Sie im Azure-Portal bereitstellen.

## <a name="on-premises-virtualization-servers"></a>Lokale Virtualisierungsserver

**Server** | **Anforderungen** | **Details**
--- | --- | ---
VMware | vCenter Server 6.7, 6.5, 6.0 oder 5.5 bzw. vSphere 6.7, 6.5, 6.0 oder 5.5 | Es wird die Verwendung eines vCenter-Servers empfohlen.<br/><br/> Ihre vSphere-Hosts und vCenter-Server sollten sich im gleichen Netzwerk befinden wie der Prozessserver. Die Prozessserverkomponenten werden standardmäßig auf dem Konfigurationsserver ausgeführt. Daher wird der Konfigurationsserver in diesem Netzwerk eingerichtet – es sei denn, Sie richten einen dedizierten Prozessserver ein.
Physisch | –

## <a name="site-recovery-configuration-server"></a>Site Recovery-Konfigurationsserver

Der Konfigurationsserver ist ein lokaler Computer, auf dem Site Recovery-Komponenten ausgeführt werden (unter anderem der Konfigurationsserver, der Prozessserver und der Masterzielserver). Für die VMware-Replikation muss der Konfigurationsserver mit sämtlichen Anforderungen eingerichtet werden. Erstellen Sie hierzu mithilfe einer OVF-Vorlage einen virtuellen VMware-Computer. Für die Replikation eines physischen Servers muss der Konfigurationsservercomputer manuell eingerichtet werden.

**Komponente** | **Anforderungen**
--- |---
CPU-Kerne | 8
RAM | 16 GB
Anzahl der Datenträger | Drei Datenträger<br/><br/> Hierzu zählen der Betriebssystemdatenträger, der Prozessservercache-Datenträger und das Aufbewahrungslaufwerk für das Failback.
Freier Speicherplatz auf dem Datenträger | 600 GB für den Prozessservercache.
Freier Speicherplatz auf dem Datenträger | 600 GB für das Aufbewahrungslaufwerk.
Betriebssystem  | Windows Server 2012 R2 oder Windows Server 2016 mit Desktopoberfläche |
Gebietsschema des Betriebssystems | Englisch (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") ist für einen Konfigurationsserver mit einer Version ab [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) nicht erforderlich.
Windows Server-Rollen | Folgende Komponenten dürfen nicht aktiviert werden: <br/> - Active Directory Domain Services <br/>- Internetinformationsdienste <br/> - Hyper-V |
Gruppenrichtlinien| Folgende Komponenten dürfen nicht aktiviert werden: <br/> - Zugriff auf Eingabeaufforderung verhindern <br/> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br/> - Vertrauenslogik für Dateianlagen <br/> - Skriptausführung aktivieren <br/> [Weitere Informationen](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Stellen Sie sicher, dass Sie:<br/><br/> - Es ist noch keine Standardwebsite vorhanden. <br/> - Die [anonyme Authentifizierung](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) ist aktiviert. <br/> - Aktivieren der Einstellung [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Es ist noch keine Website/App vorhanden, die an Port 443 lauscht.<br/>
NIC-Typ | VMXNET3 (bei Bereitstellung als VMware-VM)
Art der IP-Adresse | statischen
Ports | 443 für die Steuerkanalorchestrierung<br/>9443 für den Datentransport

## <a name="replicated-machines"></a>Replizierte Computer

Site Recovery unterstützt die Replikation beliebiger Workloads, die auf einem unterstützten Computer ausgeführt werden.

**Komponente** | **Details**
--- | ---
Computereinstellungen | Computer, die in Azure repliziert werden sollen, müssen die [Azure-Anforderungen](#azure-vm-requirements) erfüllen.
Computerworkload | Site Recovery unterstützt die Replikation beliebiger Workloads (u. a. Active Directory- und SQL Server-Workloads), die auf einem unterstützten Computer ausgeführt werden. [Weitere Informationen](https://aka.ms/asr_workload)
Windows-Betriebssystem | Windows Server 2019 (ab [Version 9.22](service-updates-how-to.md#links-to-currently-supported-update-rollups)), 64-Bit-Windows Server 2016 (Server Core, Server mit Desktopdarstellung), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 mit mindestens SP1. </br> Ab [Version 9.24](https://support.microsoft.com/en-in/help/4503156) 64-Bit-Windows 10, 64-Bit-Windows 8.1, 64-Bit-Windows 8, 64-Bit-Windows 7 (Windows 7 RTM wird nicht unterstützt)</br>  [Windows Server 2008 mit mindestens SP2 – 32-Bit und 64-Bit](migrate-tutorial-windows-server-2008.md) (nur Migration). </br></br> Windows 2016 Nano Server wird nicht unterstützt.
Architektur des Linux-Betriebssystems | Es werden nur 64-Bit-Systeme unterstützt. Ein 32-Bit-System wird nicht unterstützt.
Linux-Betriebssystem | Red Hat Enterprise Linux: 5.2 bis 5.11<b>\*\*</b>, 6.1 bis 6.10<b>\*\*</b>, 7.0 bis 7.6 <br/><br/>CentOS: 5.2 bis 5.11<b>\*\*</b>, 6.1 bis 6.10<b>\*\*</b>, 7.0 bis 7.6 <br/><br/>Ubuntu 14.04 LTS Server [(unterstützte Kernel-Versionen)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS Server [(unterstützte Kernel-Versionen)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8 [(unterstützte Kernel-Versionen)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(unterstützte Kernelversionen)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4* </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, unter dem entweder der Red Hat-kompatible Kernel oder UEK3, UEK4 und UEK5 (Unbreakable Enterprise Kernel Release 3, 4 und 5) ausgeführt wird <br/><br/></br>- Das Upgrade replizierter Computer von SUSE Linux Enterprise Server 11 SP3 auf SP4 wird nicht unterstützt. Um ein Upgrade auszuführen, deaktivieren Sie die Replikation und aktivieren Sie sie nach dem Upgrade erneut.</br></br> - [Erfahren Sie mehr](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) über die Unterstützung von Linux und Open-Source-Technologie in Azure. Site Recovery orchestriert Failover zum Ausführen von Linux-Servern in Azure. Linux-Anbieter sollten jedoch möglicherweise den Support auf Distributionsversionen einschränken, die noch nicht veraltet sind.<br/><br/> - Bei Linux-Distributionen werden nur die vordefinierten Kernel, die bei Veröffentlichungen/Updates von Nebenversionen der Distribution enthalten sind, unterstützt.<br/><br/> - Das Aktualisieren geschützter Computer über die wichtigsten Linux-Distributionsversionen hinweg wird nicht unterstützt. Um ein Upgrade auszuführen, deaktivieren Sie die Replikation, aktualisieren das Betriebssystem und aktivieren die Replikation erneut.<br/><br/> - Auf Servern mit Red Hat Enterprise Linux 5.2 bis 5.11 oder CentOS 5.2 bis 5.11 müssen die [Linux Integration Services-Komponenten (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) installiert sein, um die Computer in Azure starten zu können.


### <a name="ubuntu-kernel-versions"></a>Ubuntu-Kernelversionen


**Unterstütztes Release** | **Azure Site Recovery Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-generic bis 3.13.0-167-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-143-generic,<br/>4.15.0-1023-azure bis 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-generic bis 3.13.0-165-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-142-generic,<br/>4.15.0-1023-azure bis 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-generic bis 3.13.0-164-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-140-generic,<br/>4.15.0-1023-azure bis 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-generic bis 3.13.0-163-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-140-generic,<br/>4.15.0-1023-azure bis 4.15.0-1035-azure |
|||
16.04 LTS | [9.23][9.24 UR] | 4.4.0-21-generic bis 4.4.0-143-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-46-generic<br/>4.11.0-1009-azure bis 4.11.0-1018-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-generic bis 4.4.0-142-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-45-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-generic bis 4.4.0-140-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-43-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-generic bis 4.4.0-140-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-42-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-generic bis 4.4.0-138-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-38-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1025-azure|

### <a name="debian-kernel-versions"></a>Debian-Kernelversionen


**Unterstütztes Release** | **Azure Site Recovery Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9.24][9.24 UR]| 3.2.0-4-amd64 bis 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.21][9.21 UR],[9.22][9.22 UR],[9.23][9.23 UR], [9.24][9.24 UR] | 3.16.0-4-amd64 bis 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 – unterstützte Kernelversionen

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default bis 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default bis 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.23][9.23 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default bis 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default bis 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default bis 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.21][9.21 UR] | SP1 3.12.49-11-default bis 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default bis 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default bis 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default bis 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default bis 4.4.156-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Linux-Dateisysteme/-Gastspeicher

**Komponente** | **Unterstützt**
--- | ---
Dateisysteme | ext3, ext4, XFS
Volume-Manager | Vor [Version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM wird unterstützt. <br/> 2. /boot wird für LVM-Volume nicht unterstützt. <br/> 3. Mehrere Betriebssystemdatenträger werden nicht unterstützt.<br/><br/>Ab [Version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) wird „/boot“ für LVM unterstützt. Mehrere Betriebssystemdatenträger werden nicht unterstützt.
Paravirtualisierte Speichergeräte | Von paravirtualisierten Treibern exportierte Geräte werden nicht unterstützt.
E/A-Geräte mit Blöcken mit mehreren Warteschlangen | Nicht unterstützt.
Physische Server mit HP CCISS-Speichercontroller | Nicht unterstützt.
Benennungskonvention für Gerät/Bereitstellungspunkt | Der Gerätename oder Bereitstellungspunktname sollte eindeutig sein. Stellen Sie sicher, dass keine zwei Geräte/Bereitstellungspunkte Namen aufweisen, für die zwischen Groß-/Kleinschreibung unterschieden wird. </br> Beispiel: Es ist nicht zulässig, zwei Geräte desselben virtuellen Computers als *device1* und *Device1* zu benennen.
Verzeichnisse | Vor [Version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. Die folgenden Verzeichnisse (sofern als separate Partitionen/Dateisysteme eingerichtet) müssen sich alle auf demselben Betriebssystem-Datenträger auf dem Quellserver befinden: /(root), /boot, /usr, /usr/local, /var, /etc.</br>2. /boot muss sich auf einer Datenträgerpartition und nicht auf einem LVM-Volume befinden.<br/><br/> Ab [Version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) sind die oben genannten Einschränkungen nicht mehr gültig. „/boot“ auf LVM-Volumes über mehrere Datenträger wird nicht unterstützt.
Startverzeichnis | Mehrere Startdatenträger auf einem virtuellen Computer werden nicht unterstützt. <br/><br/> Ein Computer ohne Startdatenträger kann nicht geschützt werden.
Erforderlicher Speicherbedarf| 2 GB auf der /root-Partition <br/><br/> 250 MB im Installationsordner
XFSv5 | XFSv5-Features auf XFS-Dateisystemen wie Metadatenprüfsummen werden ab Mobility Service-Version 9.10 unterstützt. Verwenden Sie das Hilfsprogramm „xfs_info“, um den XFS-Superblock für die Partition zu überprüfen. Wenn `ftype` auf 1 festgelegt ist, werden XFSv5-Features verwendet.
BTRFS |Ab Version 9.22 wird BTRFS unterstützt, es sei denn, es liegt eine der folgenden Situationen vor:</br>Wurde das untergeordnete Volume des BTRFS-Dateisystems nach dem Aktivieren des Schutzes geändert, wird BTRFS nicht unterstützt. </br>Ist das BTRFS-Dateisystem über mehrere Datenträger verteilt, wird BTRFS nicht unterstützt.</br>Unterstützt das BTRFS-Dateisystem RAID, wird BTRFS nicht unterstützt.

## <a name="vmdisk-management"></a>VM-/Datenträgerverwaltung

**Aktion** | **Details**
--- | ---
Größe des Datenträgers auf einer replizierten VM ändern | Unterstützt.
Datenträger auf einer replizierten VM hinzufügen | Deaktivieren Sie die Replikation für die VM, fügen Sie den Datenträger hinzu, und aktivieren Sie dann erneut die Replikation. Das Hinzufügen eines Datenträgers auf einer replizierenden VM wird derzeit nicht unterstützt.

## <a name="network"></a>Netzwerk

**Komponente** | **Unterstützt**
--- | ---
NIC-Teaming im Hostnetzwerk | Unterstützt für VMware-VMs. <br/><br/>Nicht unterstützt für die Replikation physischer Computer
VLAN im Hostnetzwerk | Ja.
IPv4 im Hostnetzwerk | Ja.
IPv6 im Hostnetzwerk | Nein.
NIC-Teaming im Gast-/Servernetzwerk | Nein.
IPv4 im Gast-/Servernetzwerk | Ja.
IPv6 im Gast-/Servernetzwerk | Nein.
Statische IP im Gast-/Servernetzwerk (Windows) | Ja.
Statische IP im Gast-/Servernetzwerk (Linux) | Ja. <br/><br/>VMs werden für die Verwendung von DHCP bei Failback konfiguriert.
Mehrere NICs im Gast-/Servernetzwerk | Ja.


## <a name="azure-vm-network-after-failover"></a>Azure-VM-Netzwerk (nach Failover)

**Komponente** | **Unterstützt**
--- | ---
Azure ExpressRoute | Ja
ILB | Ja
ELB | Ja
Azure Traffic Manager | Ja
Multi-NIC | Ja
Reservierte IP-Adresse | Ja
IPv4 | Ja
Quell-IP-Adresse beibehalten | Ja
Azure Virtual Network-Dienstendpunkte<br/> | Ja
Beschleunigter Netzwerkbetrieb | Nein

## <a name="storage"></a>Storage
**Komponente** | **Unterstützt**
--- | ---
Dynamischer Datenträger | Der Betriebssystem-Datenträger muss ein Basisdatenträger sein. <br/><br/>Datenträger für Daten können dynamische Datenträger sein.
Dockerdatenträgerkonfiguration | Nein
Host-NFS | Ja für VMware<br/><br/> Nein für physische Server
Host-SAN (iSCSI/FC) | Ja
Host-vSAN | Ja für VMware<br/><br/> Nicht verfügbar für physische Server
Multipfad auf dem Host (MPIO) | Ja, getestet mit: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM für CLARiiON
Virtuelle Hostvolumes (VVols) | Ja für VMware<br/><br/> Nicht verfügbar für physische Server
Gast-/Server-VMDK | Ja
Freigegebener Gast-/Server-Clusterdatenträger | Nein
Verschlüsselter Gast-/Serverdatenträger | Nein
Gast-/Server-NFS | Nein
Gast-/Server-iSCSI | Nein
Gast/Server-SMB 3.0 | Nein
Gast-/Server-RDM | Ja<br/><br/> Nicht verfügbar für physische Server
Gast-/Serverdatenträger > 1 TB | Ja<br/><br/>Bis zu 4.095 GB<br/><br/> Der Datenträger muss größer als 1024 MB sein.
Gast-/Serverdatenträger mit einer logischen Sektorgröße von 4K und einer physischen Sektorgröße von 4k | Ja
Gast-/Serverdatenträger mit einer logischen Sektorgröße von 4K und einer physischen Sektorgröße von 512 Bytes | Ja
Gast-/Servervolume mit Stripesetdatenträgern > 4 TB <br/><br/>Logische Volumeverwaltung (Logical Volume Management, LVM)| Ja
Gast/Server – Speicherplätze | Nein
Gast/Server – Datenträger bei laufendem Systembetrieb hinzufügen/entfernen | Nein
Gast/Server – Datenträger ausschließen | Ja
Gast-/Servermultipfad (MPIO) | Nein
EFI-/UEFI-Start von Gast/Server | Unterstützt bei der Migration von virtuellen VMware-Computern oder physischen Servern unter Windows Server 2012 oder höher zu Azure<br/><br/> Sie können nur virtuelle Computer für die Migration replizieren. Ein Failback zu lokalen Ressourcen wird nicht unterstützt.<br/><br/> Der Server sollte über höchstens vier Partitionen auf dem Betriebssystem-Datenträger verfügen.<br/><br/> Erfordert Mobility Service-Version 9.13 oder höher.<br/><br/> Es wird nur NTFS unterstützt.

## <a name="replication-channels"></a>Replikationskanäle

|**Typ der Replikation**   |**Unterstützt**  |
|---------|---------|
|Ausgelagerte Datenübertragungen (ODX)    |       Nein  |
|Offlineseeding        |   Nein      |
| Azure Data Box | Nein


## <a name="azure-storage"></a>Azure-Speicher

**Komponente** | **Unterstützt**
--- | ---
Lokal redundanter Speicher | Ja
Georedundanter Speicher | Ja
Georedundanter Speicher mit Lesezugriff | Ja
Speicherebene „Kalt“ | Nein
Speicherebene „Heiß“| Nein
Blockblobs | Nein
Verschlüsselung ruhender Daten (Speicherdienstverschlüsselung)| Ja
Storage Premium | Ja
Import-/Exportdienst | Nein
Azure Storage-Firewalls für virtuelle Netzwerke, konfiguriert im Zielspeicher-/Cachespeicherkonto (zum Speichern von Replikationsdaten) | Ja
Universelle v2-Speicherkonten (heiße und kalte Ebene) | Nein

## <a name="azure-compute"></a>Azure Compute

**Feature** | **Unterstützt**
--- | ---
Verfügbarkeitsgruppen | Ja
Verfügbarkeitszonen | Nein
HUB | Ja
Verwaltete Datenträger | Ja

## <a name="azure-vm-requirements"></a>Azure-VM-Anforderungen

Lokale virtuelle Computer, die Sie in Azure replizieren, müssen die in dieser Tabelle zusammengefassten Azure-VM-Anforderungen erfüllen. Wenn Site Recovery eine Voraussetzungsprüfung durchführt, ist diese nicht erfolgreich, wenn einige der Anforderungen nicht erfüllt sind.

**Komponente** | **Anforderungen** | **Details**
--- | --- | ---
Gastbetriebssystem | Überprüfen Sie die [unterstützten Betriebssysteme](#replicated-machines) für replizierte Computer. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Architektur des Gastbetriebssystems | 64 Bit. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Bis zu 2.048 GB. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Datenträgern für Daten | Maximal 64. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Datenträgergröße | Bis zu 4.095 GB | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Freigegebene VHD | Nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
BitLocker | Nicht unterstützt. | BitLocker muss deaktiviert sein, bevor Sie die Replikation für einen Computer aktivieren. |
Name des virtuellen Computers | 1 bis 63 Zeichen.<br/><br/> Ist auf Buchstaben, Zahlen und Bindestriche beschränkt.<br/><br/> Der Computername muss mit einem Buchstaben oder einer Ziffer beginnen und enden. |  Aktualisieren Sie den Wert in den Computereigenschaften in Site Recovery.

## <a name="azure-site-recovery-churn-limits"></a>Azure Site Recovery-churn-Grenzwerte

Die folgende Tabelle enthält die Azure Site Recovery-Grenzwerte. Diese Grenzwerte basieren auf unseren Tests, können aber nicht alle möglichen E/A-Kombinationen für Anwendungen abdecken. Die tatsächlichen Ergebnisse können je nach Ihrer E/A-Mischung für die Anwendungen variieren. Um die bestmöglichen Ergebnisse zu erzielen, empfehlen wir dringend, das [Tool für die Bereitstellungsplanung](site-recovery-deployment-planner.md) auszuführen und umfangreiche Anwendungstests per Testfailover durchzuführen, um sich ein eindeutiges Bild der Anwendungsleistung zu verschaffen.

**Replikationsspeicherziel** | **Durchschnittliche E/A-Größe des Quelldatenträgers** |**Durchschnittliche Datenänderungsrate des Quelldatenträgers** | **Gesamte Datenänderungsrate des Quelldatenträgers pro Tag**
---|---|---|---
Standardspeicher | 8 KB | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 8 KB  | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 16 KB | 4 MB/s |  336 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 32 KB oder höher | 8 MB/s | 672 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 8 KB    | 5 MB/s | 421 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 16 KB oder höher |20 MB/s | 1.684 GB pro Datenträger

**Quell-Datenänderungsrate** | **Maximales Limit**
---|---
Durchschnittliche Datenänderungsrate pro VM| 25 MB/s
Spitzenänderungsrate für alle Datenträger auf einer VM | 54 MB/s
Maximale Datenänderung pro Tag, die von einem Prozessserver unterstützt wird | 2 TB

Dies sind Durchschnittswerte, bei denen eine E/A-Überlappung von 30% angenommen wird. Site Recovery kann einen höheren Durchsatz basierend auf dem Überlappungsverhältnis, höheren Schreibgrößen und dem tatsächlichen Workload-E/A-Verhalten verarbeiten. Für die obigen Zahlen wurde ein typischer Backlog von ca. fünf Minuten vorausgesetzt. Dies bedeutet, dass die Daten nach dem Hochladen verarbeitet werden und innerhalb von fünf Minuten ein Wiederherstellungspunkt erstellt wird.

## <a name="vault-tasks"></a>Tresortasks

**Aktion** | **Unterstützt**
--- | ---
Tresor über Ressourcengruppen hinweg verschieben<br/><br/> Innerhalb von und über Abonnements hinweg | Nein
Speicher, Netzwerk, Azure-VMs über Ressourcengruppen hinweg verschieben<br/><br/> Innerhalb von und über Abonnements hinweg | Nein


## <a name="download-latest-azure-site-recovery-components"></a>Herunterladen der aktuellen Azure Site Recovery-Komponenten

**Name** | **Beschreibung** | **Anweisungen zum Herunterladen der aktuellen Version**
--- | --- | ---
Konfigurationsserver | Koordiniert die Kommunikation zwischen lokalen VMware-Servern und Azure <br/><br/> Installiert auf lokalen VMware-Servern | Weitere Informationen finden Sie in den Anleitungen unter [Neuinstallation](vmware-azure-deploy-configuration-server.md) und [Upgrade vorhandener Komponente auf neueste Version](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Prozessserver|Wird standardmäßig auf dem Konfigurationsserver installiert. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage. Bei zunehmender Größe der Bereitstellung können Sie zusätzlich separate Prozessserver hinzufügen, um größere Mengen von Replikationsdatenverkehr zu bewältigen.| Weitere Informationen finden Sie in den Anleitungen unter [Neuinstallation](vmware-azure-set-up-process-server-scale.md) und [Upgrade vorhandener Komponente auf neueste Version](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Mobility Service | Koordiniert die Replikation zwischen lokalen VMware-Servern/physischen Servern und Azure/sekundärem Standort<br/><br/> Installiert auf einem virtuellen VMware-Computer oder auf physischen Servern, die Sie replizieren möchten | Weitere Informationen finden Sie in den Anleitungen unter [Neuinstallation](vmware-azure-install-mobility-service.md) und [Upgrade vorhandener Komponente auf neueste Version](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal).

Weitere Informationen zu den neuesten Features finden Sie unter [neuesten Anmerkungen zu dieser Version](https://aka.ms/ASR_latest_release_notes).


## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie](tutorial-prepare-azure.md), wie Sie Azure für die Notfallwiederherstellung von VMware-VMs vorbereiten.

[9.23 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
