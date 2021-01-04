---
title: Unterstützungsmatrix für VMware- oder physische Notfallwiederherstellung in Azure Site Recovery
description: Fasst die Unterstützung der Notfallwiederherstellung von VMware-VMs und physische Server in Azure mit Azure Site Recovery zusammen.
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 6cc67a6af9e11e5667479eadfe277d400c2d4947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492215"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Unterstützungsmatrix für die Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure

Dieser Artikel enthält eine Übersicht über die unterstützten Komponenten und Einstellungen für die Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure mit [Azure Site Recovery](site-recovery-overview.md).

- [Erfahren Sie mehr](vmware-azure-architecture.md) über die Architektur der Notfallwiederherstellung für VMware-VMs/physische Server.
- Unsere [Tutorials](tutorial-prepare-azure.md) helfen Ihnen dabei, die Notfallwiederherstellung auszuprobieren.

> [!NOTE]
> Site Recovery speichert Kundendaten nur in der Zielregion, in der die Notfallwiederherstellung für die Quellcomputer eingerichtet wurde, und verschiebt sie nicht aus dieser Region. Kunden können auf Wunsch einen Recovery Services-Tresor aus einer anderen Region auswählen. Der Recovery Services-Tresor enthält Metadaten, aber keine tatsächlichen Kundendaten.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

**Szenario** | **Details**
--- | ---
Notfallwiederherstellung von virtuellen VMware-Computern | Replikation lokaler VMware-VMs in Azure. Dieses Szenario können Sie über das Azure-Portal oder mit [PowerShell](vmware-azure-disaster-recovery-powershell.md) bereitstellen.
Notfallwiederherstellung von physischen Servern | Die Replikation lokaler physischer Windows-/Linux-Server in Azure. Dieses Szenario können Sie im Azure-Portal bereitstellen.

## <a name="on-premises-virtualization-servers"></a>Lokale Virtualisierungsserver

**Server** | **Anforderungen** | **Details**
--- | --- | ---
vCenter Server | Version 7.0 und nachfolgende Aktualisierungen in dieser Version, 6.7, 6.5, 6.0 oder 5.5 | Wir empfehlen Ihnen, einen vCenter-Server in Ihrer Notfallwiederherstellungsbereitstellung zu verwenden.
vSphere-Hosts | Version 7.0 und nachfolgende Aktualisierungen in dieser Version, 6.7, 6.5, 6.0 oder 5.5 | Ihre vSphere-Hosts und vCenter-Server sollten sich im gleichen Netzwerk befinden wie der Prozessserver. Der Prozessserver wird standardmäßig auf dem Konfigurationsserver ausgeführt. [Weitere Informationen](vmware-physical-azure-config-process-server-overview.md)

## <a name="site-recovery-configuration-server"></a>Site Recovery-Konfigurationsserver

Der Konfigurationsserver ist ein lokaler Computer, auf dem Site Recovery-Komponenten ausgeführt werden (unter anderem der Konfigurationsserver, der Prozessserver und der Masterzielserver).

- Für die VMware-VMs muss der Konfigurationsserver eingerichtet werden, indem Sie eine OVF-Vorlage herunterladen, um einen virtuellen VMware-Computer zu erstellen.
- Für physische Servers muss der Konfigurationsservercomputer manuell eingerichtet werden.

**Komponente** | **Anforderungen**
--- |---
CPU-Kerne | 8
RAM | 16 GB
Anzahl der Datenträger | Drei Datenträger<br/><br/> Hierzu zählen der Betriebssystemdatenträger, der Prozessservercache-Datenträger und das Aufbewahrungslaufwerk für das Failback.
Freier Speicherplatz auf dem Datenträger | 600 GB für den Prozessservercache.
Freier Speicherplatz auf dem Datenträger | 600 GB für das Aufbewahrungslaufwerk.
Betriebssystem  | Windows Server 2012 R2 oder Windows Server 2016 mit Desktopoberfläche <br/><br> Wenn Sie beabsichtigen, das integrierte Masterziel dieser Appliance als Failback zu verwenden, stellen Sie sicher, dass die Betriebssystemversion mindestens den replizierten Elementen entspricht.|
Gebietsschema des Betriebssystems | Englisch (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Nicht erforderlich für die Konfigurationsserverversion [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) oder höher.
Windows Server-Rollen | Aktivieren Sie nicht Active Directory Domain Services, Internetinformationsdienste (IIS) oder Hyper-V.
Gruppenrichtlinien| - Zugriff auf Eingabeaufforderung verhindern <br/> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br/> - Vertrauenslogik für Dateianlagen <br/> - Skriptausführung aktivieren <br/> - [Weitere Informationen](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | Stellen Sie sicher, dass Sie:<br/><br/> - Es ist noch keine Standardwebsite vorhanden. <br/> - Die [anonyme Authentifizierung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) ist aktiviert. <br/> - Aktivieren der Einstellung [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br/> - Es ist noch keine Website/App vorhanden, die an Port 443 lauscht.<br/>
NIC-Typ | VMXNET3 (bei Bereitstellung als VMware-VM)
Art der IP-Adresse | statischen
Ports | 443 für die Steuerkanalorchestrierung<br/>9443 für den Datentransport

## <a name="replicated-machines"></a>Replizierte Computer

Site Recovery unterstützt die Replikation beliebiger Workloads, die auf einem unterstützten Computer ausgeführt werden.

**Komponente** | **Details**
--- | ---
Computereinstellungen | Computer, die in Azure repliziert werden sollen, müssen die [Azure-Anforderungen](#azure-vm-requirements) erfüllen.
Computerworkload | Site Recovery unterstützt die Replikation beliebiger Workloads, die auf einem unterstützten Computer ausgeführt werden. [Weitere Informationen](./site-recovery-workload.md)
Computername | Stellen Sie sicher, dass der Anzeigename des Computers nicht in den Bereich der [für Azure reservierten Ressourcennamen](../azure-resource-manager/templates/error-reserved-resource-name.md) fällt.<br/><br/> Bei Namen logischer Volumes wird nicht zwischen Groß- und Kleinschreibung unterschieden. Stellen Sie sicher, dass keine zwei Volumes auf einem Gerät denselben Namen aufweisen. Beispiel: Volumes mit den Namen „voLUME1“, „volume1“ können nicht über Azure Site Recovery geschützt werden.

### <a name="for-windows"></a>Für Windows

**Betriebssystem** | **Details**
--- | ---
Windows Server 2019 | Unterstützt ab [Updaterollup 34](https://support.microsoft.com/help/4490016) (Version 9.22 des Mobility-Diensts).
Windows Server 2016 (64 Bit) | Unterstützt für Server Core, Server mit Desktopumgebung.
Windows Server 2012 R2/Windows Server 2012 | Unterstützt.
Windows Server 2008 R2 ab SP1 | Unterstützt.<br/><br/> Ab Version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) des Mobility Service-Agents müssen auf Computern, die unter Windows 2008 R2 mit SP1 oder höher ausgeführt werden, eine [Wartungsstapelaktualisierung (Servicing Stack Update, SSU)](https://support.microsoft.com/help/4490628) und ein [SHA-2-Update](https://support.microsoft.com/help/4474419) installiert sein. SHA-1 wird ab September 2019 nicht mehr unterstützt, und wenn die SHA-2-Codesignierung nicht aktiviert ist, wird die Installation bzw. das Upgrade der Agent-Erweiterung nicht ordnungsgemäß durchgeführt. Weitere Informationen zum SHA-2-Upgrade und zu den Anforderungen finden Sie [hier](https://aka.ms/SHA-2KB).
Windows Server 2008 ab SP2 (64 Bit/32 Bit) |  Nur für die Migration unterstützt. [Weitere Informationen](migrate-tutorial-windows-server-2008.md)<br/><br/> Ab Version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) des Mobility Service-Agents müssen auf Computern, die unter Windows 2008 SP2 ausgeführt werden, eine [Wartungsstapelaktualisierung (Servicing Stack Update, SSU)](https://support.microsoft.com/help/4493730) und ein [SHA-2-Update](https://support.microsoft.com/help/4474419) installiert sein. SHA-1 wird ab September 2019 nicht mehr unterstützt, und wenn die SHA-2-Codesignierung nicht aktiviert ist, wird die Installation bzw. das Upgrade der Agent-Erweiterung nicht ordnungsgemäß durchgeführt. Weitere Informationen zum SHA-2-Upgrade und zu den Anforderungen finden Sie [hier](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Windows 10, Windows 8.1, Windows 8 | Es werden nur 64-Bit-Systeme unterstützt. 32-Bit-Systeme werden nicht unterstützt.
Windows 7 mit SP1 (64 Bit) | Unterstützt ab [Updaterollup 36](https://support.microsoft.com/help/4503156) (Version 9.22 des Mobility-Diensts). </br></br> Ab Version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) des Mobility Service-Agents müssen auf Computern, die unter Windows 7 SP1 ausgeführt werden, eine [Wartungsstapelaktualisierung (Servicing Stack Update, SSU)](https://support.microsoft.com/help/4490628) und ein [SHA-2-Update](https://support.microsoft.com/help/4474419) installiert sein.  SHA-1 wird ab September 2019 nicht mehr unterstützt, und wenn die SHA-2-Codesignierung nicht aktiviert ist, wird die Installation bzw. das Upgrade der Agent-Erweiterung nicht ordnungsgemäß durchgeführt. Weitere Informationen zum SHA-2-Upgrade und zu den Anforderungen finden Sie [hier](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).

### <a name="for-linux"></a>Für Linux

**Betriebssystem** | **Details**
--- | ---
Linux | Es werden nur 64-Bit-Systeme unterstützt. 32-Bit-Systeme werden nicht unterstützt.<br/><br/>Auf allen Linux-Servern sollten [Linux Integration Services (LIS)-Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) installiert sein. Sie benötigen sie, um den Server in Azure nach einem Testfailover/Failover zu booten. Wenn integrierte LIS-Komponenten fehlen, stellen Sie sicher, dass Sie die [Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) installieren, bevor Sie die Replikation der Computer für den Start in Azure aktivieren. <br/><br/> Site Recovery orchestriert Failover zum Ausführen von Linux-Servern in Azure. Linux-Anbieter sollten jedoch möglicherweise den Support auf Distributionsversionen einschränken, die noch nicht veraltet sind.<br/><br/> Bei Linux-Distributionen werden nur die vordefinierten Kernel, die bei Veröffentlichungen/Updates von Nebenversionen der Distribution enthalten sind, unterstützt.<br/><br/> Das Aktualisieren geschützter Computer über die wichtigsten Linux-Distributionsversionen hinweg wird nicht unterstützt. Um ein Upgrade auszuführen, deaktivieren Sie die Replikation, aktualisieren das Betriebssystem und aktivieren die Replikation erneut.<br/><br/> [Erfahren Sie mehr](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) über die Unterstützung von Linux und Open-Source-Technologie in Azure.
Linux Red Hat Enterprise | 5.2 bis 5.11</b><br/> 6.1 bis 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9 (Betaversion)](https://support.microsoft.com/help/4578241/), [7.9](https://support.microsoft.com/help/4590304/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609), [8.3](https://support.microsoft.com/help/4597409/) <br/> Bei einigen älteren Kernels auf Servern mit Red Hat Enterprise Linux 5.2 bis 5.11 und 6.1 bis 6.10 sind keine [Linux Integration Services-Komponenten (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) vorinstalliert. Wenn integrierte LIS-Komponenten fehlen, stellen Sie sicher, dass Sie die [Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) installieren, bevor Sie die Replikation der Computer für den Start in Azure aktivieren.
Linux: CentOS | 5.2 bis 5.11</b><br/> 6.1 bis 6.10</b><br/> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9](https://support.microsoft.com/help/4578241/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609), [8.3](https://support.microsoft.com/help/4597409/) <br/><br/> Bei einigen älteren Kernels auf Servern mit CentOS 5.2 bis 5.11 und 6.1 bis 6.10 sind keine [Linux Integration Services-Komponenten (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) vorinstalliert. Wenn integrierte LIS-Komponenten fehlen, stellen Sie sicher, dass Sie die [Komponenten](https://www.microsoft.com/download/details.aspx?id=55106) installieren, bevor Sie die Replikation der Computer für den Start in Azure aktivieren.
Ubuntu | Ubuntu 14.04* LTS Server [(Überprüfen Sie die unterstützten Kernel-Versionen)](#ubuntu-kernel-versions)<br/>Ubuntu 16.04* LTS Server [(Überprüfen Sie die unterstützten Kernel-Versionen)](#ubuntu-kernel-versions) </br> Ubuntu 18.04* LTS Server [(Überprüfen Sie die unterstützten Kernel-Versionen)](#ubuntu-kernel-versions) </br> Ubuntu 20.04* LTS Server [(Überprüfen Sie die unterstützten Kernel-Versionen)](#ubuntu-kernel-versions) </br> (*enthält Unterstützung für alle 14.04.* x *-, 16.04.* x *-, 18.04.* x *-, 20.04.* x*-Versionen)
Debian | Debian 7/Debian 8 (bietet Unterstützung für alle 7. *x*, 8. *x*-Versionen); Debian 9 (enthält Unterstützung für 9.1 bis 9.13. Debian 9.0 wird nicht unterstützt.) [(Überprüfen Sie die unterstützten Kernel-Versionen.)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(überprüfen Sie die unterstützten Kernelversionen)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(Überprüfen Sie die unterstützten Kernelversionen)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3. [Stellen Sie sicher, dass Sie das Installationsprogramm für den aktuellen Mobilitäts-Agent auf den Konfigurationsserver herunterladen](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server). </br> SUSE Linux Enterprise Server 11 SP4 </br> **Hinweis**: Das Upgrade replizierter Computer von SUSE Linux Enterprise Server 11 SP3 auf SP4 wird nicht unterstützt. Um ein Upgrade auszuführen, deaktivieren Sie die Replikation, und aktivieren Sie sie nach dem Upgrade erneut. <br/>|
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4573888/), [7.9](https://support.microsoft.com/help/4597409/), [8.0](https://support.microsoft.com/help/4573888/), [8.1](https://support.microsoft.com/help/4573888/), [8.2](https://support.microsoft.com/help/4573888/), [8.3](https://support.microsoft.com/help/4597409/)  <br/> Mit einem Red Hat-kompatiblen Kernel oder Unbreakable Enterprise Kernel Release 3, 4 und 5 (UEK3, UEK4, UEK5)<br/><br/>8.1<br/>Die Ausführung wird auf allen UEK-Kernels und RedHat-Kernels vom Typ „<= 3.10.0-1062.*“ in [9.35](https://support.microsoft.com/help/4573888/) unterstützt. Die Unterstützung für die restlichen RedHat-Kernels ist in [9.36](https://support.microsoft.com/help/4578241/) verfügbar.

> [!Note]
>- Für jede Windows-Version unterstützt Azure Site Recovery nur [LTSC-Builds (Long-Term Servicing Channel)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc).  [Halbjährliche Kanalreleases](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) werden derzeit nicht unterstützt.
>- Stellen Sie sicher, dass Azure Site Recovery für Linux-Versionen keine benutzerdefinierten Betriebssystemimages unterstützt. Es werden nur die vordefinierten Kernel unterstützt, die bei Veröffentlichungen/Updates von Nebenversionen der Distribution enthalten sind.

### <a name="ubuntu-kernel-versions"></a>Ubuntu-Kernelversionen

**Unterstütztes Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/) | 3.13.0-24-generic bis 3.13.0-170-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-148-generic,<br/>4.15.0-1023-azure bis 4.15.0-1045-azure |
|||
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic bis 4.4.0-194-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-123-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1098-azure|
16.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic bis 4.4.0-190-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-118-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1096-azure|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic bis 4.4.0-189-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-115-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1093-azure |
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic bis 4.4.0-186-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-112-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1092-azure |
16.04 LTS | [9.35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-generic bis 4.4.0-184-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-106-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1089-azure |
|||
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic bis 4.15.0-123-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-63-generic </br> 5.3.0-19-generic bis 5.3.0-69-generic </br> 5.4.0-37-generic bis 5.4.0-53-generic</br> 4.15.0-1009-azure bis 4.15.0-1099-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1035-azure </br> 5.4.0-1020-azure bis 5.4.0-1031-azure|
18.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic bis 4.15.0-118-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-61-generic </br> 5.3.0-19-generic bis 5.3.0-67-generic </br> 5.4.0-37-generic bis 5.4.0-48-generic</br> 4.15.0-1009-azure bis 4.15.0-1096-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1035-azure </br> 5.4.0-1020-azure bis 5.4.0-1026-azure|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic bis 4.15.0-115-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-60-generic </br> 5.3.0-19-generic bis 5.3.0-66-generic </br> 5.4.0-37-generic bis 5.4.0-45-generic</br> 4.15.0-1009-azure bis 4.15.0-1093-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1035-azure </br> 5.4.0-1020-azure bis 5.4.0-1023-azure|
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic bis 4.15.0-112-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-58-generic </br> 5.3.0-19-generic bis 5.3.0-64-generic </br> 5.4.0-37-generic bis 5.4.0-42-generic</br> 4.15.0-1009-azure bis 4.15.0-1092-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1032-azure </br> 5.4.0-1020-azure bis 5.4.0-1022-azure|
18.04 LTS | [9.35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-generic bis 4.15.0-108-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-52-generic </br> 5.3.0-19-generic bis 5.3.0-61-generic </br> 4.15.0-1009-azure bis 4.15.0-1089-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1031-azure|
|||
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic bis 5.4.0-53 </br> -generic 5.4.0-1010-azure bis 5.4.0-1031-azure
20.04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic bis 5.4.0-48 </br> -generic 5.4.0-1010-azure bis 5.4.0-1026-azure
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic bis 5.4.0-45 </br> -generic 5.4.0-1010-azure bis 5.4.0-1023-azure
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic bis 5.4.0-42 </br> -generic 5.4.0-1010-azure bis 5.4.0-1022-azure

### <a name="debian-kernel-versions"></a>Debian-Kernelversionen


**Unterstütztes Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/)| 3.2.0-4-amd64 bis 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/) | 3.16.0-4-amd64 bis 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 bis 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 bis 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 bis 4.19.0-0.bpo.12-cloud-amd64 </br> Debian 9.1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 bis 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 bis 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 bis 4.19.0-0.bpo.11-cloud-amd64 </br> 
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 bis 4.9.0-13-amd64, 4.19.0-0.bpo.6-amd64 bis 4.19.0-0.bpo.10-amd64, 4.19.0-0.bpo.6-cloud-amd64 bis 4.19.0-0.bpo.10-cloud-amd64

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 – unterstützte Kernelversionen

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.39](https://support.microsoft.com/help/4597409/) | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.34-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/),  | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.22-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.35](https://support.microsoft.com/help/4573888/) | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.19-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 – unterstützte Kernelversionen

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure bis 5.3.18-18.21-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure bis 5.3.18-18.18-azure
SUSE Linux Enterprise Server 15 und 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.38-azure
SUSE Linux Enterprise Server 15 und 15 SP1 | [9.35](https://support.microsoft.com/help/4573888/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.33-azure 

## <a name="linux-file-systemsguest-storage"></a>Linux-Dateisysteme/-Gastspeicher

**Komponente** | **Unterstützt**
--- | ---
Dateisysteme | ext3, ext4, XFS, BTRFS (geltende Bedingungen gemäß dieser Tabelle)
Bereitstellung der Verwaltung logischer Volumes (Logical Volume Management, LVM)| Vollständige Speicherzuweisung: ja <br></br> Schlanke Speicherzuweisung: nein
Volume-Manager | – LVM wird unterstützt.<br/> – /boot auf LVM wird ab [Update Rollup 31](https://support.microsoft.com/help/4478871/) (Version 9.20 des Mobilitätsdiensts) unterstützt. Es wird in früheren Versionen des Mobilitätsdiensts nicht unterstützt.<br/> – Mehrere Betriebssystemdatenträger werden nicht unterstützt.
Paravirtualisierte Speichergeräte | Von paravirtualisierten Treibern exportierte Geräte werden nicht unterstützt.
E/A-Geräte mit Blöcken mit mehreren Warteschlangen | Wird nicht unterstützt.
Physische Server mit HP CCISS-Speichercontroller | Wird nicht unterstützt.
Benennungskonvention für Gerät/Bereitstellungspunkt | Der Gerätename oder Bereitstellungspunktname sollte eindeutig sein.<br/> Stellen Sie sicher, dass keine zwei Geräte/Bereitstellungspunkte Namen aufweisen, für die zwischen Groß-/Kleinschreibung unterschieden wird. So ist es beispielsweise nicht zulässig, zwei Geräte für denselben virtuellen Computer mit *device1* und *Device1* zu benennen.
Verzeichnisse | Wenn Sie eine frühere Version des Mobilitätsdiensts als Version 9.20 (veröffentlich in [Update Rollup 31](https://support.microsoft.com/help/4478871/)) verwenden, dann gelten die folgenden Einschränkungen:<br/><br/> – Diese Verzeichnisse (sofern als separate Partitionen/Dateisysteme eingerichtet) müssen sich auf demselben Betriebssystemdatenträger auf dem Quellserver befinden: /(root), /boot, /usr, /usr/local, /var, /etc.</br> – Das /boot-Verzeichnis muss sich auf einer Datenträgerpartition und nicht auf einem LVM-Volume befinden.<br/><br/> Ab Version 9.20 gelten diese Einschränkungen nicht. 
Startverzeichnis | – Startdatenträger dürfen nicht im GPT-Partitionsformat vorliegen. Dies ist eine Einschränkung der Azure-Architektur. GPT-Datenträger werden als Datenträger für Daten unterstützt.<br/><br/> Mehrere Startdatenträger auf einem virtuellen Computer werden nicht unterstützt.<br/><br/> – /boot auf LVM-Volumes über mehrere Datenträger wird nicht unterstützt.<br/> – Ein Computer ohne einen Startdatenträger kann nicht repliziert werden.
Erforderlicher Speicherbedarf| 2 GB auf der /root-Partition <br/><br/> 250 MB im Installationsordner
XFSv5 | XFSv5-Features auf XFS-Dateisystemen, z.B. die Metadatenprüfsumme, werden unterstützt (ab Version 9.10 des Mobilitätsdiensts).<br/> Verwenden Sie das Hilfsprogramm „xfs_info“, um den XFS-Superblock für die Partition zu überprüfen. Wenn `ftype` auf 1 festgelegt ist, werden XFSv5-Features verwendet.
BTRFS | BTRFS wird ab [Update Rollup 34](https://support.microsoft.com/help/4490016) (Version 9.22 des Mobilitätsdiensts) unterstützt. BTRFS wird nicht unterstützt, wenn:<br/><br/> – Das untergeordnete Volume des BTRFS-Dateisystems nach dem Aktivieren des Schutzes geändert wird.</br> – Das BTRFS-Dateisystem über mehrere Datenträger verteilt wird.</br> – Die BTRFS-Dateisystem RAID unterstützt.

## <a name="vmdisk-management"></a>VM-/Datenträgerverwaltung

**Aktion** | **Details**
--- | ---
Größe des Datenträgers auf einer replizierten VM ändern | Wird vor einem Failover direkt in den VM-Eigenschaften der Quell-VM unterstützt. Es besteht keine Notwendigkeit, die Replikation zu deaktivieren bzw. erneut zu aktivieren.<br/><br/> Wenn Sie die Quell-VM nach einem Failover ändern, werden die Änderungen nicht erfasst.<br/><br/> Wenn Sie die Datenträgergröße der Azure-VM nach dem Failover ändern, erstellt Site Recovery beim Failback einen neuen virtuellen Computer mit den Aktualisierungen.
Datenträger auf einer replizierten VM hinzufügen | Wird nicht unterstützt.<br/> Deaktivieren Sie die Replikation für die VM, fügen Sie den Datenträger hinzu, und aktivieren Sie dann erneut die Replikation.

> [!NOTE]
> Jede Änderung der Datenträgeridentität wird nicht unterstützt. Wenn z. B. die Datenträgerpartitionierung von GPT in MBR oder umgekehrt geändert wurde, wird die Datenträgeridentität dadurch nicht geändert. In einem solchen Szenario wird die Replikation unterbrochen und ein neues Setup ist erforderlich. 

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
Private Link-Zugriff auf den Site Recovery-Dienst | Ja. [Weitere Informationen](hybrid-how-to-enable-replication-private-endpoints.md)


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
Azure-VNET-Dienstendpunkte<br/> | Ja
Beschleunigte Netzwerke | Nein

## <a name="storage"></a>Storage
**Komponente** | **Unterstützt**
--- | ---
Dynamischer Datenträger | Der Betriebssystemdatenträger muss ein Basisdatenträger sein. <br/><br/>Datenträger für Daten können dynamische Datenträger sein.
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
Gast-/Server-iSCSI | Für Migration: Ja<br/>Für Notfallwiederherstellung: Nein, iSCSI führt ein Failback als angefügter Datenträger auf den virtuellen Computer aus
Gast/Server-SMB 3.0 | Nein
Gast-/Server-RDM | Ja<br/><br/> Nicht verfügbar für physische Server
Gast-/Serverdatenträger > 1 TB | Ja, der Datenträger muss größer als 1024 MB sein.<br/><br/>Bis zu 8.192 GB bei der Replikation auf verwaltete Datenträger (ab Version 9.26)<br></br> Bis zu 4.095 GB bei der Replikation in Speicherkonten
Gast-/Serverdatenträger mit einer logischen Sektorgröße von 4K und einer physischen Sektorgröße von 4k | Nein
Gast-/Serverdatenträger mit einer logischen Sektorgröße von 4K und einer physischen Sektorgröße von 512 Bytes | Nein
Gast-/Servervolume mit Stripesetdatenträgern > 4 TB | Ja
Logische Volumeverwaltung (Logical Volume Management, LVM)| Vollständige Speicherzuweisung: ja <br></br> Schlanke Speicherzuweisung: nein
Gast/Server – Speicherplätze | Nein
Gast/Server – Datenträger bei laufendem Systembetrieb hinzufügen/entfernen | Nein
Gast/Server – Datenträger ausschließen | Ja
Gast-/Servermultipfad (MPIO) | Nein
GPT-Partitionen von Gast/Server | Fünf Partitionen werden ab [Update Rollup 37](https://support.microsoft.com/help/4508614/) (Version 9.25 des Mobilitätsdiensts) unterstützt. Bisher wurden vier unterstützt.
ReFS | Resilient File System (Robustes Dateisystem) wird ab Version 9.23 des Mobilitätsdiensts unterstützt.
EFI-/UEFI-Start von Gast/Server | – Wird für alle [UEFI-Betriebssysteme in Azure Marketplace](../virtual-machines/generation-2.md#generation-2-vm-images-in-azure-marketplace) mit Version 9.30 und höher des Mobilitäts-Agent von Site Recovery unterstützt <br/> – Sichere UEFI-Starttypen werden nicht unterstützt. [Weitere Informationen.](../virtual-machines/generation-2.md#on-premises-vs-azure-generation-2-vms)

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
Verschlüsselung ruhender Daten (SSE)| Ja
Verschlüsselung ruhender Daten (CMK)| Ja (über PowerShell ab Az-Modulversion 3.3.0)
Doppelte Verschlüsselung im Ruhezustand | Ja (über PowerShell ab Az-Modulversion 3.3.0). Weitere Informationen zu unterstützten Regionen für [Windows](../virtual-machines/disk-encryption.md) und [Linux](../virtual-machines/disk-encryption.md) erhalten Sie unter den jeweiligen Links.
Storage Premium | Ja
Option für die sichere Übertragung | Ja
Import-/Exportdienst | Nein
Azure Storage-Firewalls für VNets | Ja.<br/> Konfiguriert in Zielspeicher-/Cachespeicherkonto (zum Speichern von Replikationsdaten).
Allgemeine v2-Speicherkonten (heiße und kalte Ebene) | Ja (Transaktionskosten sind wesentlich höher für V2 als für V1)

## <a name="azure-compute"></a>Azure Compute

**Feature** | **Unterstützt**
--- | ---
Verfügbarkeitsgruppen | Ja
Verfügbarkeitszonen | Nein
HUB | Ja
Verwaltete Datenträger | Ja

## <a name="azure-vm-requirements"></a>Azure-VM-Anforderungen

Lokale virtuelle Computer, die in Azure repliziert werden, müssen die in dieser Tabelle zusammengefassten Azure-VM-Anforderungen erfüllen. Wenn Site Recovery eine Voraussetzungsprüfung für die Replikation durchführt, ist diese nicht erfolgreich, wenn einige der Anforderungen nicht erfüllt sind.

**Komponente** | **Anforderungen** | **Details**
--- | --- | ---
Gastbetriebssystem | Überprüfen Sie die [unterstützten Betriebssysteme](#replicated-machines) für replizierte Computer. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Architektur des Gastbetriebssystems | 64 Bit. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Bis zu 2.048 GB. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 </br> Start- und Systempartition auf unterschiedlichen Datenträgern werden nicht unterstützt | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Datenträgern für Daten | Maximal 64. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Datenträgergröße | Bis zu 8.192 GB bei der Replikation auf verwaltete Datenträger (ab Version 9.26)<br></br>Bis zu 4.095 GB bei der Replikation in Speicherkonten| Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Freigegebene VHD | Wird nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Wird nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
BitLocker | Wird nicht unterstützt. | BitLocker muss deaktiviert sein, bevor Sie die Replikation für einen Computer aktivieren. |
Name des virtuellen Computers | 1 bis 63 Zeichen.<br/><br/> Ist auf Buchstaben, Zahlen und Bindestriche beschränkt.<br/><br/> Der Computername muss mit einem Buchstaben oder einer Ziffer beginnen und enden. |  Aktualisieren Sie den Wert in den Computereigenschaften in Site Recovery.

## <a name="resource-group-limits"></a>Grenzwerte für Ressourcengruppen

Informationen zur Anzahl virtueller Computer, die unter einer einzelnen Ressourcengruppe geschützt werden können, finden Sie im Artikel zu [Grenzwerten und Kontingenten für Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).

## <a name="churn-limits"></a>Änderungsgrenzwerte

Die folgende Tabelle enthält die Azure Site Recovery-Grenzwerte.
- Diese Grenzwerte basieren auf unseren Tests, können aber nicht alle möglichen E/A-Kombinationen für Anwendungen abdecken.
- Die tatsächlichen Ergebnisse können je nach Ihrer E/A-Mischung für die Anwendungen variieren.
- Um die bestmöglichen Ergebnisse zu erzielen, empfehlen wir dringend, das Sie das [Bereitstellungsplanertool](site-recovery-deployment-planner.md) ausführen und umfangreiche Anwendungstests per Testfailover durchführen, um sich ein eindeutiges Bild der Anwendungsleistung zu verschaffen.

**Replikationsziel** | **Durchschnittliche E/A-Größe des Quelldatenträgers** |**Durchschnittliche Datenänderungsrate des Quelldatenträgers** | **Gesamte Datenänderungsrate des Quelldatenträgers pro Tag**
---|---|---|---
Standardspeicher | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 16 KB | 4 MB/s |    336 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 32 KB oder höher | 8 MB/s | 672 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 8 KB    | 5 MB/s | 421 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 16 KB oder höher |20 MB/s | 1\.684 GB pro Datenträger


**Quell-Datenänderungsrate** | **Maximales Limit**
---|---
Spitzenänderungsrate für alle Datenträger auf einer VM | 54 MB/s
Maximale Datenänderung pro Tag, die von einem Prozessserver unterstützt wird | 2 TB

- Dies sind Durchschnittswerte, bei denen eine E/A-Überlappung von 30% angenommen wird.
- Site Recovery kann einen höheren Durchsatz basierend auf dem Überlappungsverhältnis, höheren Schreibgrößen und dem tatsächlichen Workload-E/A-Verhalten verarbeiten.
- Für diese Zahlen wurde ein typischer Backlog von ca. fünf Minuten vorausgesetzt. Dies bedeutet, dass die Daten nach dem Hochladen verarbeitet werden und innerhalb von fünf Minuten ein Wiederherstellungspunkt erstellt wird.

## <a name="vault-tasks"></a>Tresortasks

**Aktion** | **Unterstützt**
--- | ---
Tresor über Ressourcengruppen hinweg verschieben | Nein
Tresor innerhalb von und über Abonnements hinweg verschieben | Nein
Speicher, Netzwerk, Azure-VMs über Ressourcengruppen hinweg verschieben | Nein
Speicher, Netzwerk, Azure-VMs innerhalb von und über Abonnements hinweg verschieben | Nein


## <a name="obtain-latest-components"></a>Erhalten der neusten Komponenten

**Name** | **Beschreibung** | **Details**
--- | --- | ---
Konfigurationsserver | Lokal installiert.<br/> Koordiniert die Kommunikation zwischen lokalen VMware-Servern oder physischen Computern und Azure. | - [Erfahren Sie mehr über](vmware-physical-azure-config-process-server-overview.md) den Konfigurationsserver.<br/> - [Erfahren Sie mehr über](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) das Upgrade auf die neuste Version.<br/> - [Erfahren Sie mehr über](vmware-azure-deploy-configuration-server.md) die Einrichtung des Konfigurationsservers.
Prozessserver | Wird standardmäßig auf dem Konfigurationsserver installiert.<br/> Empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure.<br/> Bei zunehmender Größe der Bereitstellung können Sie zusätzliche Prozessserver hinzufügen, um größere Mengen von Replikationsdatenverkehr zu bewältigen. | - [Erfahren Sie mehr über](vmware-physical-azure-config-process-server-overview.md) den Prozessserver.<br/> - [Erfahren Sie mehr über](vmware-azure-manage-process-server.md#upgrade-a-process-server) das Upgrade auf die neuste Version.<br/> - [Erfahren Sie mehr über](vmware-physical-large-deployment.md#set-up-a-process-server) die Einrichtung horizontal skalierter Prozessserver.
Mobility Service | Installiert auf einem virtuellen VMware-Computer oder auf physischen Servern, die Sie replizieren möchten.<br/> Koordiniert die Replikation zwischen lokalen VMware-Servern/physischen Servern und Azure.| - [Erfahren Sie mehr über](vmware-physical-mobility-service-overview.md) den Mobilitätsdienst.<br/> - [Erfahren Sie mehr über](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) das Upgrade auf die neuste Version.<br/>



## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie](tutorial-prepare-azure.md), wie Sie Azure für die Notfallwiederherstellung von VMware-VMs vorbereiten.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery