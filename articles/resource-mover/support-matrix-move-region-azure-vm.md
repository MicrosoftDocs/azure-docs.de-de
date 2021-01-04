---
title: Unterstützungsmatrix für das Verschieben von virtuellen Azure-Computern in eine andere Region mithilfe von Azure Resource Mover
description: Informieren Sie sich über die Unterstützung für das Verschieben von virtuellen Azure-Computern zwischen Regionen mithilfe von Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 10/11/2020
ms.author: raynew
ms.openlocfilehash: 4da707ab698599c8ea5dd8e1ea8647f543eb2a68
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524248"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Unterstützung für das Verschieben von virtuellen Azure-Computern zwischen Azure-Regionen

Dieser Artikel enthält Informationen zur Unterstützung und zu den Voraussetzungen für das Verschieben von virtuellen Computern und der entsprechenden Netzwerkressourcen in Azure-Regionen mithilfe von Resource Mover.

> [!IMPORTANT]
> Azure Resource Mover ist derzeit als Vorschauversion verfügbar.


## <a name="windows-vm-support"></a>Unterstützung von virtuellen Computern unter Windows

Resource Mover unterstützt virtuelle Azure-Computer, die unter den folgenden Windows-Betriebssystemen ausgeführt werden.

**Betriebssystem** | **Details**
--- | ---
Windows Server 2019 | Unterstützt für Server Core, Server mit Desktopumgebung.
Windows Server 2016  | Unterstützt für Server Core, Server mit Desktopdarstellung.
Windows Server 2012 R2 | Unterstützt.
Windows Server 2012 | Unterstützt.
Windows Server 2008 R2 mit SP1/SP2 | Unterstützt.<br/><br/> Bei Computern, die unter Windows Server 2008 R2 mit SP1/SP2 ausgeführt werden, müssen Sie ein Windows [Wartungsstapelaktualisierung (Servicing Stack Update, SSU)](https://support.microsoft.com/help/4490628) und ein [SHA-2-Update](https://support.microsoft.com/help/4474419) installieren.  SHA-1 wird ab September 2019 nicht mehr unterstützt, und wenn die SHA-2-Codesignierung nicht aktiviert ist, wird die Installation bzw. das Upgrade der Agent-Erweiterung nicht ordnungsgemäß durchgeführt. Weitere Informationen zum SHA-2-Upgrade und zu den Anforderungen finden Sie [hier](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Unterstützt.
Windows 8.1 (x64) | Unterstützt.
Windows 8 (x64) | Unterstützt.
Windows 7 (x64) mit SP1 und höher | Installieren Sie eine Windows [Wartungsstapelaktualisierung (Servicing Stack Update, SSU)](https://support.microsoft.com/help/4490628) und ein [SHA-2-Update](https://support.microsoft.com/help/4474419) auf Computern, die unter Windows 7 mit SP1 ausgeführt werden.  SHA-1 wird seit September 2019 nicht mehr unterstützt, und wenn die SHA-2-Codesignierung nicht aktiviert ist, wird der Vorbereitungsschritt nicht erfolgreich durchgeführt. Weitere Informationen zum SHA-2-Upgrade und zu den Anforderungen finden Sie [hier](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Unterstützung von virtuellen Computern unter Linux

Resource Mover unterstützt virtuelle Azure-Computer, die unter den folgenden Linux-Betriebssystemen ausgeführt werden.

**Betriebssystem** | **Details**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [Unterstützte Kernel-Versionen](#supported-ubuntu-kernel-versions)
Ubuntu 16.04 LTS Server | [Unterstützte Kernel-Version](#supported-ubuntu-kernel-versions)<br/><br/> Bei Ubuntu-Servern mit kennwortbasierter Authentifizierung und Anmeldung und mit dem Paket „cloud-init“ zum Konfigurieren von Cloud-VMs kann bei einem Failover die kennwortbasierte Anmeldung deaktiviert sein (je nach cloud-init-Konfiguration). Die kennwortbasierte Anmeldung kann auf der VM wieder aktiviert werden, indem (für die VM, für die das Failover ausgeführt wurde) das Kennwort im Azure-Portal unter „Support“ > „Problembehandlung“ im Menü „Einstellungen“ zurückgesetzt wird.
Ubuntu 18.04 LTS Server | [Unterstützte Kernelversion](#supported-ubuntu-kernel-versions)
Debian 7 | [Unterstützte Kernelversionen](#supported-debian-kernel-versions)
Debian 8 | [Unterstützte Kernelversionen](#supported-debian-kernel-versions)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [Unterstützte Kernel-Versionen](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 und 15 SP1 [(Unterstützte Kernel-Versionen)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Mit einem Red Hat-kompatiblen Kernel oder Unbreakable Enterprise Kernel Release 3, 4 und 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Unterstützte Ubuntu-Kernelversionen

**Release** | **Kernelversion** 
--- | --- 
14.04 LTS |  3.13.0-24-generic bis 3.13.0-170-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-148-generic,<br/>4.15.0-1023-azure bis 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-generic bis 4.4.0-171-generic<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-74-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-generic bis 4.15.0-74-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-37-generic </br> 5.3.0-19-generic bis 5.3.0-24-generic </br> 4.15.0-1009-azure bis 4.15.0-1037-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1028-azure </br> 5.3.0-1007-azure bis 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>Unterstützte Debian-Kernelversionen 

**Release** |  **Kernelversion** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 bis 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64 bis 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64 bis 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>Unterstützte SUSE Linux Enterprise Server 12-Kernelversionen 

**Release** | **Kernelversion** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>Unterstützte SUSE Linux Enterprise Server 15-Kernelversionen

**Release** | **Kernelversion** |
--- |  --- |
SUSE Linux Enterprise Server 15 und 15 SP1 |  Alle SUSE 15- und 15-Stock-Kernel werden unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>Unterstützte Linux-Dateisysteme/-Gastspeicher

* Dateisysteme: ext3, ext4, XFS, BTRFS
* Volume-Manager: LVM2
* Multipfadsoftware: Gerätezuordnung


## <a name="supported-vm-compute-settings"></a>Unterstützte VM-Computeeinstellungen

**Einstellung** | **Unterstützung** | **Details**
--- | --- | ---
Size | Jede Größe von virtuellen Azure-Computern mit mindestens zwei CPU-Kernen und 1 GB RAM | Überprüfen Sie die [Größen der virtuellen Azure-Computer](../virtual-machines/sizes-general.md).
Verfügbarkeitsgruppen | Unterstützt | Unterstützt.
Verfügbarkeitszonen | Unterstützt | Wird je nach Unterstützung der Zielregion unterstützt.
Azure-Katalogimages (von Microsoft veröffentlicht) | Unterstützt | Wird unterstützt, wenn auf der VM ein unterstütztes Betriebssystem ausgeführt wird.
Azure-Katalogimages (von Drittanbietern veröffentlicht)  | Unterstützt | Wird unterstützt, wenn auf der VM ein unterstütztes Betriebssystem ausgeführt wird.
Benutzerdefinierte Images (von Drittanbietern veröffentlicht)| Unterstützt | Wird unterstützt, wenn auf der VM ein unterstütztes Betriebssystem ausgeführt wird.
Virtuelle Computer, auf denen Site Recovery verwendet wird | Nicht unterstützt | Verschieben Sie im Back-End mithilfe von Site Recovery Ressourcen in Regionen für virtuelle Computer. Wenn Sie Site Recovery bereits verwenden, deaktivieren Sie die Replikation, und starten Sie anschließend den Vorbereitungsprozess.
Azure RBAC-Richtlinien | Nicht unterstützt | Richtlinien für die rollenbasierte Zugriffssteuerung in Azure (Role Based Access Control, RBAC) auf virtuellen Computern werden nicht auf den virtuellen Computer in der Zielregion kopiert.
Erweiterungen | Nicht unterstützt | Erweiterungen werden nicht in den virtuellen Computer in der Zielregion kopiert. Daher müssen Sie sie nach dem Verschieben manuell installieren.


## <a name="supported-vm-storage-settings"></a>Unterstützte Einstellungen für den Speicher von virtuellen Computern

In dieser Tabelle ist die Unterstützung für den Betriebssystemdatenträger, Datenträger und temporären Datenträger der Azure-VM zusammengefasst. Es ist wichtig, dass die VM-Datenträgergrenzwerte und -Ziele für [Linux](../virtual-machines/linux/disk-scalability-targets.md)- und [Windows](../virtual-machines/windows/disk-scalability-targets.md)-VMs eingehalten werden, um Leistungsprobleme zu vermeiden.

> [!NOTE]
> Der virtuelle Zielcomputer muss gleich groß wie oder größer als der virtuelle Quellcomputer sein. Für die Validierung werden folgende Parameter verwendet: Anzahl der Datenträger, Anzahl der Netzwerkkarten, Verfügbare CPUs, Arbeitsspeicher in GB. Trifft dies nicht zu, wird ein Fehler ausgegeben.


**Komponente** | **Unterstützung** | **Details**
--- | --- | ---
Maximale Größe des Betriebssystemdatenträgers | 2\.048 GB | [Erfahren Sie mehr](../virtual-machines/managed-disks-overview.md) zu VM-Datenträgern.
Temporärer Datenträger | Nicht unterstützt | Der temporäre Datenträger ist immer vom Vorbereitungsprozess ausgeschlossen.<br/><br/> Speichern Sie auf dem temporären Datenträger keine persistenten Daten. [Weitere Informationen](../virtual-machines/managed-disks-overview.md#temporary-disk)
Maximale Größe des Datenträgers | 8\.192 GB für verwaltete Datenträger
Minimale Größe des Datenträgers |  2 GB für verwaltete Datenträger |
Maximale Anzahl von Datenträgern | Bis zu 64, gemäß der Unterstützung für eine bestimmte Azure-VM-Größe | [Erfahren Sie mehr](../virtual-machines/sizes.md) zu VM-Größen.
Änderungsrate für Datenträger | Maximal 10 MBit/s pro Datenträger für Storage Premium. Maximal 2 MBit/s pro Datenträger für Standardspeicher. | Wenn die durchschnittliche Datenänderungsrate auf dem Datenträger dauerhaft über dem Maximalwert liegt, kann dies durch die Vorbereitung nicht aufgeholt werden.<br/><br/>  Falls der Maximalwert aber nur sporadisch überschritten wird, kann die Vorbereitung aufholen, aber es kommt ggf. zu einer leichten Verzögerung bei den Wiederherstellungspunkten.
Datenträger (Standard-Speicherkonto) | Wird nicht unterstützt. | Geben Sie als Speichertyp „Verwalteter Datenträger“ an, und versuchen Sie dann, den virtuellen Computer zu verschieben.
Datenträger (Premium-Speicherkonto) | Nicht unterstützt | Geben Sie als Speichertyp „Verwalteter Datenträger“ an, und versuchen Sie dann, den virtuellen Computer zu verschieben.
Verwalteter Datenträger (Standard) | Unterstützt  |
Verwalteter Datenträger (Premium) | Unterstützt |
SSD Standard | Unterstützt |
Generation 2 (UEFI-Start) | Unterstützt
Startdiagnose-Speicherkonto | Nicht unterstützt | Aktivieren Sie es nach dem Verschieben des virtuellen Computers in die Zielregion erneut.

### <a name="limits-and-data-change-rates"></a>Grenzwerte und Datenänderungsraten

In der folgenden Tabelle sind die auf unseren Tests basierenden Grenzwerte aufgeführt. Diese decken nicht alle möglichen E/A-Kombinationen für Anwendungen ab. Die tatsächlichen Ergebnisse variieren je nach Ihrer E/A-Mischung für die Anwendungen. Zwei Grenzwerte müssen beachtet werden, nämlich jene für Datenänderungen bei Datenträgern und jene für Datenänderungen bei virtuellen Computern.

**Speicherziel** | **Durchschnittliche E/A-Größe des Quelldatenträgers** |**Durchschnittliche Datenänderungsrate des Quelldatenträgers** | **Gesamte Datenänderungsrate des Quelldatenträgers pro Tag**
---|---|---|---
Standardspeicher | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 16 KB | 4 MB/s |    336 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 32 KB oder höher | 8 MB/s | 672 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 8 KB    | 5 MB/s | 421 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 16 KB oder höher |20 MB/s | 1\.684 GB pro Datenträger

## <a name="supported-vm-networking-settings"></a>Unterstützte SQL-VM-Netzwerkeinstellungen

**Einstellung** | **Unterstützung** | **Details**
--- | --- | ---
NIC | Unterstützt | Geben Sie eine vorhandene Ressource in der Zielregion an, oder erstellen Sie während der Vorbereitungsprozesses eine neue Ressource. 
Interner Lastenausgleich | Unterstützt | Geben Sie eine vorhandene Ressource in der Zielregion an, oder erstellen Sie während der Vorbereitungsprozesses eine neue Ressource.  
Öffentlicher Load Balancer | Derzeit nicht unterstützt | Geben Sie eine vorhandene Ressource in der Zielregion an, oder erstellen Sie während der Vorbereitungsprozesses eine neue Ressource.  
Öffentliche IP-Adresse | Unterstützt | Geben Sie eine vorhandene Ressource in der Zielregion an, oder erstellen Sie während der Vorbereitungsprozesses eine neue Ressource.<br/><br/> Die öffentliche IP-Adresse ist regionsspezifisch und bleibt nach dem Verschieben nicht in der Zielregion erhalten. Beachten Sie dies, wenn Sie die Netzwerkeinstellungen (einschließlich Regeln für den Lastenausgleich) am Zielstandort ändern.
Netzwerksicherheitsgruppe | Unterstützt | Geben Sie eine vorhandene Ressource in der Zielregion an, oder erstellen Sie während der Vorbereitungsprozesses eine neue Ressource.  
Reservierte (statische) IP-Adresse | Unterstützt | Diese Option kann derzeit nicht konfiguriert werden. Der Wert ist standardmäßig auf den Quellwert festgelegt. <br/><br/> Wenn die Netzwerkkarte auf dem virtuellen Quellcomputer über eine statische IP-Adresse verfügt und für das Zielsubnetz die gleiche IP-Adresse verfügbar ist, wird sie dem virtuellen Zielcomputer zugewiesen.<br/><br/> Wenn diese IP-Adresse beim Zielsubnetz nicht verfügbar ist, tritt beim Einleiten der Verschiebung für den virtuellen Computer ein Fehler auf.
Dynamische IP-Adresse | Unterstützt | Diese Option kann derzeit nicht konfiguriert werden. Der Wert ist standardmäßig auf den Quellwert festgelegt.<br/><br/> Wenn die Netzwerkkarte auf der Quelle über eine dynamische IP-Adressierung verfügt, ist die Netzwerkkarte auf dem virtuellen Zielcomputer standardmäßig ebenfalls dynamisch.
IP-Konfigurationen | Unterstützt | Diese Option kann derzeit nicht konfiguriert werden. Der Wert ist standardmäßig auf den Quellwert festgelegt.

## <a name="outbound-access-requirements"></a>Anforderungen für den ausgehenden Zugriff

Virtuelle Azure-Computer, die verschoben werden sollen, benötigen ausgehenden Zugriff.


### <a name="url-access"></a>URL-Zugriff

 Lassen Sie den Zugriff auf die folgenden URLs zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden:

**Name** | **Öffentliche Azure-Cloud** | **Details** 
--- | --- | --- 
Storage | `*.blob.core.windows.net`  | Ermöglicht das Schreiben von Daten aus der VM in das Cachespeicherkonto in der Quellregion 
Azure Active Directory | `login.microsoftonline.com`  | Stellt die Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs bereit. 
Replikation | `*.hypervrecoverymanager.windowsazure.com` | Ermöglicht die Kommunikation der VM mit Site Recovery 
Service Bus | `*.servicebus.windows.net` | Ermöglicht es der VM, die Site Recovery-Überwachung und -Diagnosedaten zu schreiben 

## <a name="nsg-rules"></a>NSG-Regeln
Wenn Sie Regeln für Netzwerksicherheitsgruppen (NSG) zum Steuern der ausgehenden Verbindung verwenden, erstellen Sie diese [Diensttagregeln](../virtual-network/service-tags-overview.md). Alle Regeln müssen den ausgehenden Zugriff auf HTTPS (443) erlauben.
- Erstellen Sie eine Storage-Tagregel für die Quellregion.
- Erstellen Sie eine *AzureSiteRecovery*-Tagregel, um den Zugriff auf den Site Recovery-Dienst in allen Regionen zu ermöglichen. Dieses Tag weist Abhängigkeiten mit diesen anderen Tags auf. Daher müssen Sie für diese ebenfalls Regeln erstellen:
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Es wird empfohlen, Regeln zu testen, jedoch nicht in einer Produktionsumgebung. [Sehen Sie sich einige Beispiele an](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, einen virtuellen Azure-Computer mit Resource Mover in eine andere Region zu [verschieben](tutorial-move-region-virtual-machines.md).