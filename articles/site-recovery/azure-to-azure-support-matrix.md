---
title: Unterstützungsmatrix für die Azure-VM-Notfallwiederherstellung mit Azure Site Recovery
description: Fasst die Unterstützung für die Notfallwiederherstellung für virtuelle Azure-Computer in einer sekundären Region mit Azure Site Recovery zusammen.
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 8123dbc18ad2fc162b9b92ae40920417e6b874ce
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763280"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Unterstützungsmatrix für die Notfallwiederherstellung von Azure-VMs zwischen Azure-Regionen

Dieser Artikel fasst die Unterstützung und die Voraussetzungen für die Notfallwiederherstellung von Azure-VMs aus einer Azure-Region in einer anderen mit dem Dienst [Azure Site Recovery](site-recovery-overview.md) zusammen.


## <a name="deployment-method-support"></a>Unterstützung für die Bereitstellungsmethode

**Bereitstellung** |  **Unterstützung**
--- | ---
**Azure portal** | Unterstützt.
**PowerShell** | Unterstützt. [Weitere Informationen](azure-to-azure-powershell.md)
**REST-API** | Unterstützt.
**BEFEHLSZEILENSCHNITTSTELLE (CLI)** | Derzeit nicht unterstützt


## <a name="resource-support"></a>Ressourcenunterstützung

**Ressourcenaktion** | **Details**
--- | ---
**Tresore über Ressourcengruppen hinweg verschieben** | Nicht unterstützt
**Verschieben von Compute-, Speicher- und Netzwerkressourcen über Ressourcengruppen hinweg** | Wird nicht unterstützt.<br/><br/> Wenn Sie eine VM oder zugehörige Komponenten wie Speicher bzw. Netzwerke verschieben, nachdem die VM repliziert wurde, müssen Sie die Replikation für die VM deaktivieren und dann wieder aktivieren.
**Replizieren von Azure-VMs von einem Abonnement in ein anderes zur Notfallwiederherstellung** | Innerhalb des gleichen Azure Active Directory-Mandanten unterstützt.
**Migrieren von VMs zwischen Regionen innerhalb von unterstützten geografischen Clustern (innerhalb von Abonnements und übergreifend)** | Innerhalb des gleichen Azure Active Directory-Mandanten unterstützt.
**Migrieren von VMs in derselben Region** | Wird nicht unterstützt.

## <a name="region-support"></a>Unterstützung für Regionen

Sie können virtuelle Computer zwischen zwei beliebigen Regionen im gleichen geografischen Cluster replizieren. Geographische Cluster werden unter Berücksichtigung von Datenlatenz und Souveränität definiert.


**Geografischer Cluster** | **Azure-Regionen**
-- | --
Südamerika | Kanada, Osten; Kanada, Mitte; USA, Süden-Mitte; USA, Westen-Mitte; USA, Osten; USA, Osten 2; USA, Westen; USA, Westen 2; USA, Mitte; USA, Norden-Mitte
Europa | Vereinigtes Königreich, Westen; Vereinigtes Königreich, Süden; Europa, Norden; Europa, Westen; Südafrika, Westen; Südafrika, Norden; Norwegen, Osten; Norwegen, Westen; Frankreich, Mitte; Schweiz, Norden
Asia | Indien, Süden; Indien, Mitte; Indien, Westen; Asien, Südosten; Asien, Osten; Japan, Osten; Japan, Westen; Korea, Mitte; Korea, Süden
Australien    | Australien, Osten; Australien, Südosten; Australien, Mitte; Australien, Mitte 2
Azure Government    | „US GOV Virginia“; „US GOV Iowa“; „US GOV Arizona“; „US GOV Texas“; „US DoD, Osten“; „US DoD, Mitte“
Deutschland    | „Deutschland, Mitte“; „Deutschland, Nordosten“
China | China, Osten; China, Norden; China, Norden 2; China, Osten 2
Eingeschränkte Regionen, die für eine Notfallwiederherstellung innerhalb des Landes reserviert sind |„Deutschland, Norden“ reserviert für Kunden in „Deutschland, Westen-Mitte“, „Schweiz, Westen“ reserviert für Kunden in „Schweiz, Norden“, „Frankreich, Süden“ reserviert für Kunden in „Frankreich, Mitte“, „VAE, Mitte“eingeschränkt für Kunden in „VAE, Norden“

>[!NOTE]
>
> - Für **Brasilien, Süden** können Sie eine Replikation und ein Failover zu diesen Regionen durchführen: USA, Süden-Mitte; USA, Westen-Mitte; USA, Osten; USA, Osten 2; USA, Westen; USA, Westen 2 und USA, Norden-Mitte.
> - „Brasilien Süd“ kann nur als Quellregion verwendet werden, von der aus virtuelle Computer mithilfe von Site Recovery replizieren können. Es kann nicht als Zielregion dienen. Dies liegt an Latenzproblemen aufgrund der geografischen Entfernungen. Beachten Sie Folgendes: Wenn Sie ein Failover von „Brasilien, Süden“ als Quellregion zu einem Ziel ausführen, wird ein Failback zu „Brasilien, Süden“ aus der Zielregion unterstützt.
> - Sie können innerhalb von Regionen arbeiten, auf die Sie entsprechenden Zugriff haben.
> - Wenn Sie die Region nicht sehen können, in der Sie einen Tresor erstellen möchten, dann stellen Sie sicher, dass Ihr Abonnement in dieser Region Zugriff auf das Erstellen von Ressourcen hat.
> - Wenn Sie beim Aktivieren der Replikation eine Region in einem geografischen Cluster nicht sehen können, stellen Sie sicher, dass Ihr Abonnement über Berechtigungen zum Erstellen von virtuellen Computern in dieser Region verfügt.



## <a name="cache-storage"></a>Cachespeicher

In dieser Tabelle ist die Unterstützung für das Cachespeicherkonto zusammengefasst, das von Site Recovery während der Replikation verwendet wird.

**Einstellung** | **Unterstützung** | **Details**
--- | --- | ---
Allgemeine V2-Speicherkonten (heiße und kalte Ebene) | Unterstützt | Die Nutzung von GPv2 wird nicht empfohlen, da die Transaktionskosten für V2- erheblich höher als für V1-Speicherkonten sind.
Storage Premium | Nicht unterstützt | Standard-Speicherkonten werden für den Cachespeicher verwendet, um die Kosten zu optimieren.
Azure Storage-Firewalls für virtuelle Netzwerke  | Unterstützt | Wenn Sie ein Cache- oder Zielspeicherkonto mit aktivierter Firewall verwenden, müssen Sie [vertrauenswürdigen Microsoft-Diensten den Zugriff erlauben](../storage/common/storage-network-security.md#exceptions), indem Sie die entsprechende Option auswählen.<br></br>Stellen Sie außerdem sicher, dass Sie den Zugriff auf mindestens ein Subnetz des Quell-VNET zulassen.


## <a name="replicated-machine-operating-systems"></a>Replizierte Computerbetriebssysteme

Site Recovery unterstützt die Replikation von Azure-VMs, auf denen die in diesem Abschnitt angegebenen Betriebssysteme ausgeführt werden. Hinweis: Wenn für einen Computer mit aktiver Replikation ein Upgrade (oder Downgrade) auf einen anderen Hauptkernel durchgeführt wird, muss die Replikation nach dem Upgrade deaktiviert und anschließend wieder aktiviert werden.

### <a name="windows"></a>Windows


**Betriebssystem** | **Details**
--- | ---
Windows Server 2019 | Unterstützt für Server Core, Server mit Desktopumgebung.
Windows Server 2016  | Unterstützt für Server Core, Server mit Desktopdarstellung.
Windows Server 2012 R2 | Unterstützt.
Windows Server 2012 | Unterstützt.
Windows Server 2008 R2 mit SP1/SP2 | Unterstützt.<br/><br/> Ab Version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) der Mobility Service-Erweiterung für virtuelle Azure-Computer müssen auf Computern unter Windows Server 2008 R2 SP1/SP2 eine [Windows-Wartungsstapelaktualisierung (Servicing Stack Update, SSU)](https://support.microsoft.com/help/4474419) und ein [SHA-2-Update](https://support.microsoft.com/help/4490628) installiert werden.  SHA-1 wird ab September 2019 nicht mehr unterstützt, und wenn die SHA-2-Codesignierung nicht aktiviert ist, wird die Installation bzw. das Upgrade der Agent-Erweiterung nicht ordnungsgemäß durchgeführt. Weitere Informationen zum SHA-2-Upgrade und zu den Anforderungen finden Sie [hier](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Unterstützt.
Windows 8.1 (x64) | Unterstützt.
Windows 8 (x64) | Unterstützt.
Windows 7 (x64) mit SP1 und höher | Ab Version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) der Mobility Service-Erweiterung für virtuelle Azure-Computer müssen auf Computern unter Windows 7 mit SP1 eine [Windows-Wartungsstapelaktualisierung (Servicing Stack Update, SSU)](https://support.microsoft.com/help/4474419) und ein [SHA-2-Update](https://support.microsoft.com/help/4490628) installiert werden.  SHA-1 wird ab September 2019 nicht mehr unterstützt, und wenn SHA-2-Codesignierung nicht aktiviert ist, wird die Installation bzw. das Upgrade der Agent-Erweiterung nicht ordnungsgemäß durchgeführt. Weitere Informationen zum SHA-2-Upgrade und zu den Anforderungen finden Sie [hier](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Betriebssystem** | **Details**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4564347/), [7.9](https://support.microsoft.com/help/4578241/), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1, [8.2](https://support.microsoft.com/help/4570609/), [8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10 </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, [7.8](https://support.microsoft.com/help/4564347/), [7.9 Vor-GA-Version](https://support.microsoft.com/help/4578241/), 7.9 GA-Version wird von 9.37 Hotfix-Patch unterstützt** </br> 8.0, 8.1, [8.2](https://support.microsoft.com/en-us/help/4570609), [8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | Schließt Unterstützung für alle 14.04.*x*-Versionen ein. [Unterstützte Kernelversionen](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) 
Ubuntu 16.04 LTS Server | Schließt Unterstützung für alle 16.04.*x*-Versionen ein. [Unterstützte Kernelversion](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Bei Ubuntu-Servern mit kennwortbasierter Authentifizierung und Anmeldung und mit dem Paket „cloud-init“ zum Konfigurieren von Cloud-VMs kann bei einem Failover die kennwortbasierte Anmeldung deaktiviert sein (je nach cloud-init-Konfiguration). Die kennwortbasierte Anmeldung kann auf der VM wieder aktiviert werden, indem (für die VM, für die das Failover ausgeführt wurde) das Kennwort im Azure-Portal unter „Support“ > „Problembehandlung“ im Menü „Einstellungen“ zurückgesetzt wird.
Ubuntu 18.04 LTS Server | Schließt Unterstützung für alle 18.04.*x*-Versionen ein. [Unterstützte Kernelversion](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Bei Ubuntu-Servern mit kennwortbasierter Authentifizierung und Anmeldung und mit dem Paket „cloud-init“ zum Konfigurieren von Cloud-VMs kann bei einem Failover die kennwortbasierte Anmeldung deaktiviert sein (je nach cloud-init-Konfiguration). Die kennwortbasierte Anmeldung kann auf der VM wieder aktiviert werden, indem (für die VM, für die das Failover ausgeführt wurde) das Kennwort im Azure-Portal unter „Support“ > „Problembehandlung“ im Menü „Einstellungen“ zurückgesetzt wird.
Ubuntu 20.04 LTS Server | Schließt Unterstützung für alle 20.04.*x*-Versionen ein. [Unterstützte Kernelversion](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Schließt Unterstützung für alle 7er Versionen ein. *x*-Versionen [Unterstützte Kernelversionen](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Schließt Unterstützung für alle 8er Versionen ein. *x*-Versionen [Unterstützte Kernelversionen](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Schließt Unterstützung für 9.1 bis 9.13 ein. Debian 9.0 wird nicht unterstützt. [Unterstützte Kernel-Versionen](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5 [(Unterstützte Kernelversionen)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2 [(Unterstützte Kernelversionen)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Ein Upgrade von replizierenden Computern von SP3 auf SP4 wird nicht unterstützt. Wenn ein replizierter Computer aktualisiert wurde, müssen Sie die Replikation deaktivieren und nach dem Upgrade dann wieder aktivieren.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7.8](https://support.microsoft.com/help/4573888/), [7.9](https://support.microsoft.com/help/4597409), [8.0](https://support.microsoft.com/help/4573888/), [8.1](https://support.microsoft.com/help/4573888/) (Mit einem Red Hat-kompatiblen Kernel oder Unbreakable Enterprise Kernel Release 3, 4 und 5 (UEK3, UEK4, UEK5))<br/><br/>8.1 (Das Ausführen wird auf allen UEK-Kernels und RedHat-Kernels <= 3.10.0-1062.* in [9.35](https://support.microsoft.com/help/4573888/) unterstützt. Die Unterstützung für den Rest der RedHat-Kernel ist in [9.36](https://support.microsoft.com/help/4578241/) verfügbar.)

> [!NOTE]
> Für Linux-Versionen unterstützt Azure Site Recovery keine benutzerdefinierten Betriebssystemimages. Es werden nur die vordefinierten Kernel unterstützt, die bei Veröffentlichungen/Updates von Nebenversionen der Distribution enthalten sind.

** Hinweis: Zusätzlich zur neuesten Version des Mobilitäts-Agents gibt es ein Azure Site Recovery-Rollout eines Hotfix-Patches, um die neuesten Linux-Kernel innerhalb von 15 Tagen nach Veröffentlichung zu unterstützen. Der Rollout dieses Fixes erfolgt zwischen zwei Hauptversionen. Befolgen Sie zum Aktualisieren auf die aktuelle Version von Mobility Agent (einschließlich des Hotfix-Patches) die Schritte in [diesem Artikel](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Ein Rollout dieses Patches erfolgt derzeit für Mobility Agent-Instanzen im Azure-zu-Azure DR-Szenario.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Unterstützte Ubuntu-Kernelversionen für virtuelle Azure-Computer

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/)| 3.13.0-24-generic bis 3.13.0-170-generic,<br/>3.16.0-25-generic bis 3.16.0-77-generic,<br/>3.19.0-18-generic bis 3.19.0-80-generic,<br/>4.2.0-18-generic bis 4.2.0-42-generic,<br/>4.4.0-21-generic bis 4.4.0-148-generic,<br/>4.15.0-1023-azure bis 4.15.0-1045-azure |
|||
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic bis 4.4.0-194-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-123-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1098-azure|
16.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic bis 4.4.0-190-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-118-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1096-azure </br> 4.4.0-193-generic, 4.15.0-120-generic, 4.15.0-122-generic, 4.15.0-1098-azure bis Hotfix-Patch 9.38**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic bis 4.4.0-189-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-115-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1093-azure </br> 4.4.0-190-generic, 4.15.0-117-generic, 4.15.0-118-generic, 4.15.0-1095-azure, 4.15.0-1096-azure bis 9.37 Hotfix-Patch**|
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic bis 4.4.0-187-generic,<br/>4.8.0-34-generic bis 4.8.0-58-generic,<br/>4.10.0-14-generic bis 4.10.0-42-generic,<br/>4.11.0-13-generic bis 4.11.0-14-generic,<br/>4.13.0-16-generic bis 4.13.0-45-generic,<br/>4.15.0-13-generic bis 4.15.0-112-generic<br/>4.11.0-1009-azure bis 4.11.0-1016-azure,<br/>4.13.0-1005-azure bis 4.13.0-1018-azure <br/>4.15.0-1012-azure bis 4.15.0-1092-azure |
|||
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic bis 4.15.0-123-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-63-generic </br> 5.3.0-19-generic bis 5.3.0-69-generic </br> 5.4.0-37-generic bis 5.4.0-53-generic</br> 4.15.0-1009-azure bis 4.15.0-1099-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1035-azure </br> 5.4.0-1020-azure bis 5.4.0-1031-azure|
18.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic bis 4.15.0-118-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-61-generic </br> 5.3.0-19-generic bis 5.3.0-67-generic </br> 5.4.0-37-generic bis 5.4.0-48-generic</br> 4.15.0-1009-azure bis 4.15.0-1096-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1035-azure </br> 5.4.0-1020-azure bis 5.4.0-1026-azure </br> 4.15.0-121-generic, 4.15.0-122-generic, 5.0.0-62-generic, 5.3.0-68-generic, 5.4.0-51-generic, 5.4.0-52-generic, 4.15.0-1099-azure, 5.4.0-1031-azure bis Hotfix-Patch 9.38**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic bis 4.15.0-115-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-60-generic </br> 5.3.0-19-generic bis 5.3.0-66-generic </br> 5.4.0-37-generic bis 5.4.0-45-generic</br> 4.15.0-1009-azure bis 4.15.0-1093-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1035-azure </br> 5.4.0-1020-azure bis 5.4.0-1023-azure</br> 4.15.0-117-generic, 4.15.0-118-generic, 5.0.0-61-generic, 5.3.0-67-generic, 5.4.0-47-generic, 5.4.0-48-generic, 4.15.0-1095-azure, 4.15.0-1096-azure, 5.4.0-1025-azure, 5.4.0-1026-azure bis 9.37 Hotfix-Patch**|
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic bis 4.15.0-112-generic </br> 4.18.0-13-generic bis 4.18.0-25-generic </br> 5.0.0-15-generic bis 5.0.0-58-generic </br> 5.3.0-19-generic bis 5.3.0-65-generic </br> 5.4.0-37-generic bis 5.4.0-42-generic</br> 4.15.0-1009-azure bis 4.15.0-1092-azure </br> 4.18.0-1006-azure bis 4.18.0-1025-azure </br> 5.0.0-1012-azure bis 5.0.0-1036-azure </br> 5.3.0-1007-azure bis 5.3.0-1032-azure </br> 5.4.0-1020-azure bis 5.4.0-1022-azure </br> 5.0.0-60-generic & 5.3.0-1035-azure bis Hotfix-Patch 9.36**|
|||
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic bis 5.4.0-53 </br> -generic 5.4.0-1010-azure bis 5.4.0-1031-azure
20.04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic bis 5.4.0-48 </br> -generic 5.4.0-1010-azure bis 5.4.0-1026-azure </br> 5.4.0-51-generic, 5.4.0-52-generic, 5.8.0-23-generic, 5.8.0-25-generic, 5.4.0-1031-azure bis 9.38 Hotfix-Patch**
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic bis 5.4.0-45 </br> -generic 5.4.0-1010-azure bis 5.4.0-1023-azure </br> 5.4.0-47-generic, 5.4.0-48-generic, 5.4.0-1025-azure, 5.4.0-1026-azure bis 9.37 Hotfix-Patch**
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic bis 5.4.0-42 </br> -generic 5.4.0-1010-azure bis 5.4.0-1022-azure

** Hinweis: Zusätzlich zur neuesten Version des Mobilitäts-Agents gibt es ein Azure Site Recovery-Rollout eines Hotfix-Patches, um die neuesten Linux-Kernel innerhalb von 15 Tagen nach Veröffentlichung zu unterstützen. Der Rollout dieses Fixes erfolgt zwischen zwei Hauptversionen. Befolgen Sie zum Aktualisieren auf die aktuelle Version von Mobility Agent (einschließlich des Hotfix-Patches) die Schritte in [diesem Artikel](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Ein Rollout dieses Patches erfolgt derzeit für Mobility Agent-Instanzen im Azure-zu-Azure DR-Szenario.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Unterstützte Debian-Kernelversionen für virtuelle Azure-Computer

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9.35](https://support.microsoft.com/help/4573888/), [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/) | 3.2.0-4-amd64 bis 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.35](https://support.microsoft.com/help/4573888/, ), [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/), [9.38](https://support.microsoft.com/help/4590304/), [9.39](https://support.microsoft.com/help/4597409/) | 3.16.0-4-amd64 bis 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64 bis 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 bis 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 bis 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 bis 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 bis 4.19.0-0.bpo.12-cloud-amd64 </br> 
Debian 9.1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 bis 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 bis 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 bis 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0-0.bpo.12-amd64, 4.19.0-0.bpo.12-cloud-amd64 bis 9.38 Hotfix-Patch**
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 bis 4.9.0-13-amd64, 4.19.0-0.bpo.6-amd64 bis 4.19.0-0.bpo.10-amd64, 4.19.0-0.bpo.6-cloud-amd64 bis 4.19.0-0.bpo.10-cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Unterstützte SUSE Linux Enterprise Server 12-Kernelversionen für Azure-VMs

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.39](https://support.microsoft.com/help/4597409/) | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.34-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/) | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.22-azure </br> 4.12.14-16.25-azure, 4.12.14-16.28-azure bis 9.37 Hotfix-Patch**|
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4, SP5) | [9.35](https://support.microsoft.com/help/4573888/) | Alle [SUSE 12 SP1-, SP2-, SP3- und SP4-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) werden unterstützt.</br></br> 4.4.138-4.7-azure bis 4.4.180-4.31-azure</br>4.12.14-6.3-azure bis 4.12.14-6.43-azure </br> 4.12.14-16.7-azure bis 4.12.14-16.19-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Unterstützte SUSE Linux Enterprise Server 15-Kernelversionen für Azure-VMs

**Release** | **Mobility Service-Version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure bis 5.3.18-18.21-azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure bis 5.3.18-18.18-azure </br> 4.12.14-8.47-azure, 5.3.18-18.21-azure bis Hotfix-Patch 9.38**
SUSE Linux Enterprise Server 15 und 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/), [9.37](https://support.microsoft.com/help/4582666/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.38-azure </br> 4.12.14-8.41-azure, 4.12.14-8.44-azure bis 9.37 Hotfix-Patch**
SUSE Linux Enterprise Server 15 und 15 SP1 | [9.35](https://support.microsoft.com/help/4573888/)  | Standardmäßig werden alle [SUSE 15- und 15-Stock-Kernel](https://www.suse.com/support/kb/doc/?id=000019587) unterstützt.</br></br> 4.12.14-5.5-azure bis 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure bis 4.12.14-8.33-azure 


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replizierte Computer – Linux-Dateisystem/Gastspeicher

* Dateisysteme: ext3, ext4, XFS, BTRFS
* Volume-Manager: LVM2

> [!NOTE]
> Multipfadsoftware wird nicht unterstützt.


## <a name="replicated-machines---compute-settings"></a>Replizierte Computer – Compute-Einstellungen

**Einstellung** | **Unterstützung** | **Details**
--- | --- | ---
Size | Jede Größe von virtuellen Azure-Computern mit mindestens 2 CPU-Kernen und 1 GB RAM | Überprüfen Sie die [Größen der virtuellen Azure-Computer](../virtual-machines/sizes.md).
Verfügbarkeitsgruppen | Unterstützt | Wenn Sie die Replikation für eine Azure-VM mit den Standardoptionen aktivieren, wird basierend auf den Einstellungen der Quellregion automatisch eine Verfügbarkeitsgruppe erstellt. Sie können diese Einstellungen ändern.
Verfügbarkeitszonen | Unterstützt |
Hybridnutzungsvorteil (Hybrid Use Benefit, HUB) | Unterstützt | Wenn für den virtuellen Quellcomputer eine HUB-Lizenz aktiviert wurde, verwendet auch die Testfailover- oder Failover-VM die HUB-Lizenz.
VM-Skalierungsgruppen | Nicht unterstützt |
Azure-Katalogimages – von Microsoft veröffentlicht | Unterstützt | Wird unterstützt, wenn auf der VM ein unterstütztes Betriebssystem ausgeführt wird.
Azure-Katalogimages – von Drittanbietern veröffentlicht | Unterstützt | Wird unterstützt, wenn auf der VM ein unterstütztes Betriebssystem ausgeführt wird.
Benutzerdefinierte Images – von Drittanbietern veröffentlicht | Unterstützt | Wird unterstützt, wenn auf der VM ein unterstütztes Betriebssystem ausgeführt wird.
Mit Site Recovery migrierte virtuelle Computer | Unterstützt | Wenn eine VMware-VM oder ein physischer Computer mit Site Recovery zu Azure migriert wurde, müssen Sie die ältere Version des Mobility Service deinstallieren, die auf dem Computer ausgeführt wird, und den Computer neu starten, bevor er in einer anderen Azure-Region repliziert werden kann.
Azure RBAC-Richtlinien | Nicht unterstützt | Richtlinien für die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) auf virtuellen Computern werden nicht in die Failover-VM in der Zielregion repliziert.
Erweiterungen | Nicht unterstützt | Erweiterungen werden nicht auf die Failover-VM in der Zielregion repliziert. Nach dem Failover müssen sie manuell installiert werden.
Näherungsplatzierungsgruppen | Unterstützt | Virtuelle Computer innerhalb einer Näherungsplatzierungsgruppe können mithilfe von Site Recovery geschützt werden.


## <a name="replicated-machines---disk-actions"></a>Replizierte Computer – Datenträgeraktionen

**Aktion** | **Details**
-- | ---
Größe des Datenträgers auf einer replizierten VM ändern | Wird vor dem Failover auf der Quell-VM unterstützt. Es besteht keine Notwendigkeit, die Replikation zu deaktivieren bzw. erneut zu aktivieren.<br/><br/> Wenn Sie die Quell-VM nach einem Failover ändern, werden die Änderungen nicht erfasst.<br/><br/> Wenn Sie die Datenträgergröße auf dem virtuellen Azure-Computer nach einem Failover ändern, werden die Änderungen von Site Recovery nicht erfasst, und das Failback erfolgt auf die ursprüngliche VM-Größe.
Hinzufügen eines Datenträgers zu einem replizierten virtuellen Computer | Unterstützt
Offlineänderungen an geschützten Datenträgern | Wenn Sie Datenträger trennen und offline Änderungen vornehmen, muss eine vollständige Neusynchronisierung ausgelöst werden.

## <a name="replicated-machines---storage"></a>Replizierte Computer – Speicher

In dieser Tabelle ist die Unterstützung für den Betriebssystemdatenträger, Datenträger und temporären Datenträger der Azure-VM zusammengefasst.

- Es ist wichtig, dass die VM-Datenträgergrenzwerte und -Ziele für [Linux](../virtual-machines/linux/disk-scalability-targets.md)- und [Windows](../virtual-machines/windows/disk-scalability-targets.md)-VMs eingehalten werden, um Leistungsprobleme zu vermeiden.
- Wenn Sie für die Bereitstellung die Standardeinstellungen verwenden, erstellt Site Recovery Datenträger und Speicherkonten automatisch basierend auf den Einstellungen für die Datenquelle.
- Achten Sie darauf, dass Sie die Richtlinien befolgen, wenn Sie Anpassungen vornehmen möchten.

**Komponente** | **Unterstützung** | **Details**
--- | --- | ---
Maximale Größe des Betriebssystemdatenträgers | 2\.048 GB | [Erfahren Sie mehr](../virtual-machines/managed-disks-overview.md) zu VM-Datenträgern.
Temporärer Datenträger | Nicht unterstützt | Der temporäre Datenträger ist immer von der Replikation ausgeschlossen.<br/><br/> Speichern Sie auf dem temporären Datenträger keine persistenten Daten. [Weitere Informationen](../virtual-machines/managed-disks-overview.md)
Maximale Größe des Datenträgers | 32 TB für verwaltete Datenträger<br></br>4 TB für nicht verwaltete Datenträger|
Minimale Größe des Datenträgers | Keine Einschränkung für nicht verwaltete Datenträger. 2 GB für verwaltete Datenträger |
Maximale Anzahl von Datenträgern | Bis zu 64, gemäß der Unterstützung für eine bestimmte Azure-VM-Größe | [Erfahren Sie mehr](../virtual-machines/sizes.md) zu VM-Größen.
Änderungsrate für Datenträger | Maximal 20 MBit/s pro Datenträger für Storage Premium. Maximal 2 MBit/s pro Datenträger für Standardspeicher. | Wenn die durchschnittliche Datenänderungsrate auf dem Datenträger dauerhaft über dem Maximalwert liegt, kann dies durch die Replikation nicht aufgeholt werden.<br/><br/>  Falls der Maximalwert aber nur sporadisch überschritten wird, kann die Replikation aufholen, aber es kommt ggf. zu einer leichten Verzögerung bei den Wiederherstellungspunkten.
Datenträger – Standard-Speicherkonto | Unterstützt |
Datenträger – Storage Premium-Konto | Unterstützt | Wenn ein virtueller Computer Datenträger in Premium- und Standard-Speicherkonten aufweist, können Sie für jeden Datenträger ein eigenes Zielspeicherkonto auswählen, um sicherzustellen, dass die gleiche Speicherkonfiguration in der Zielregion vorhanden ist.
Verwalteter Datenträger – Standard | Unterstützt in Azure-Regionen, in denen Azure Site Recovery unterstützt wird. |
Verwalteter Datenträger – Premium | Unterstützt in Azure-Regionen, in denen Azure Site Recovery unterstützt wird. |
SSD Standard | Unterstützt |
Redundanz | LRS und GRS werden unterstützt.<br/><br/> ZRS wird nicht unterstützt.
Kalter und heißer Speicher | Nicht unterstützt | VM-Datenträger werden für kalten und heißen Speicher nicht unterstützt
Speicherplätze | Unterstützt |
Verschlüsselung ruhender Daten (SSE) | Unterstützt | SSE ist die Standardeinstellung für Speicherkonten.
Verschlüsselung ruhender Daten (CMK) | Unterstützt | Software- und HSM-Schlüssel werden für verwaltete Datenträger unterstützt.
Doppelte Verschlüsselung im Ruhezustand | Unterstützt | Erfahren Sie mehr über unterstützte Regionen für [Windows](../virtual-machines/disk-encryption.md) und [Linux](../virtual-machines/disk-encryption.md).
Azure Disk Encryption (ADE) für Windows | Unterstützt für virtuelle Computer mit verwalteten Datenträgern. | Virtuelle Computer mit nicht verwalteten Datenträgern werden nicht unterstützt. <br/><br/> Durch HSM geschützte Schlüssel werden nicht unterstützt. <br/><br/> Die Verschlüsselung einzelner Volumes auf einem einzelnen Datenträger wird nicht unterstützt. |
Azure Disk Encryption (ADE) für Linux | Unterstützt für virtuelle Computer mit verwalteten Datenträgern. | Virtuelle Computer mit nicht verwalteten Datenträgern werden nicht unterstützt. <br/><br/> Durch HSM geschützte Schlüssel werden nicht unterstützt. <br/><br/> Die Verschlüsselung einzelner Volumes auf einem einzelnen Datenträger wird nicht unterstützt. <br><br> Bekanntes Problem beim Aktivieren der Replikation. [Weitere Informationen.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS-Schlüsselrotation | Nicht unterstützt | Wenn der SAS-Schlüssel für Speicherkonten gedreht wird, muss der Kunde die Replikation deaktivieren und erneut aktivieren. |
Hinzufügen von Datenträgern im laufendem Betrieb    | Unterstützt | Die Aktivierung der Replikation für einen Datenträger, den Sie einer replizierten Azure-VM hinzufügen, wird für VMs unterstützt, die verwaltete Datenträger verwenden. <br/><br/> Es kann jeweils nur ein virtueller Datenträger einer Azure-VM im laufenden Betrieb hinzugefügt werden. Das parallele Hinzufügen mehrerer Datenträger wird nicht unterstützt. |
Entfernen von Datenträgern im laufendem Betrieb    | Nicht unterstützt | Wenn Sie Datenträger auf dem virtuellen Computer entfernen, müssen Sie die Replikation deaktivieren und dann für den virtuellen Computer erneut aktivieren.
Ausschließen von Datenträgern | Unterstützung. Zum Konfigurieren muss [PowerShell](azure-to-azure-exclude-disks.md) verwendet werden. |    Temporäre Datenträger sind standardmäßig ausgeschlossen.
Speicherplätze direkt  | Für absturzkonsistente Wiederherstellungspunkte unterstützt. Anwendungskonsistente Wiederherstellungspunkte werden nicht unterstützt. |
Dateiserver mit horizontaler Skalierung  | Für absturzkonsistente Wiederherstellungspunkte unterstützt. Anwendungskonsistente Wiederherstellungspunkte werden nicht unterstützt. |
DRBD | Datenträger, die Teil eines DRBD-Setups sind, werden nicht unterstützt. |
LRS | Unterstützt |
GRS | Unterstützt |
RA-GRS | Unterstützt |
ZRS | Nicht unterstützt |
Kalter und heißer Speicher | Nicht unterstützt | Datenträger für virtuelle Computer werden auf kaltem und heißem Speicher nicht unterstützt.
Azure Storage-Firewalls für virtuelle Netzwerke  | Unterstützt | Wenn Sie den Zugriff auf virtuelle Netzwerke in Speicherkonten einschränken, lassen Sie [vertrauenswürdige Microsoft-Dienste](../storage/common/storage-network-security.md#exceptions) zu.
Allgemeine V2-Speicherkonten (heiße und kalte Ebene) | Unterstützt | Die Transaktionskosten steigen gegenüber allgemeinen V1-Speicherkonten erheblich an.
Generation 2 (UEFI-Start) | Unterstützt
NVMe-Datenträger | Nicht unterstützt
Freigegebene Azure-Datenträger | Nicht unterstützt
Option für die sichere Übertragung | Unterstützt
Datenträger mit aktivierter Schreibbeschleunigung | Nicht unterstützt

>[!IMPORTANT]
> Um Leistungsprobleme zu vermeiden, stellen Sie sicher, dass Sie die Skalierbarkeits- und Leistungsziele für VM-Datenträger für virtuelle [Linux](../virtual-machines/linux/disk-scalability-targets.md)- oder [Windows](../virtual-machines/windows/disk-scalability-targets.md)-Computer beachten. Wenn Sie die Standardeinstellungen verwenden, erstellt Site Recovery die erforderlichen Datenträger und Speicherkonten auf Basis der Quellkonfiguration. Wenn Sie Ihre eigenen Einstellungen anpassen und verwenden möchten, halten Sie die Skalierbarkeits- und Leistungsziele für Datenträger für Ihre virtuellen Quellcomputer ein.

## <a name="limits-and-data-change-rates"></a>Grenzwerte und Datenänderungsraten

Die folgende Tabelle enthält die Site Recovery-Grenzwerte.

- Diese Grenzwerte basieren auf unseren Tests, können aber begreiflicherweise nicht alle möglichen E/A-Kombinationen für Anwendungen abdecken.
- Die tatsächlichen Ergebnisse können je nach Ihrer E/A-Mischung für die App variieren.
- Zwei Grenzwerte müssen beachtet werden, nämlich jene für Datenänderungen bei Datenträgern und jene für Datenänderungen bei virtuellen Computern.
- Der aktuelle Grenzwert für die Datenänderungsrate pro virtuellem Computer beträgt 54 MB/s, unabhängig von der Größe.

**Speicherziel** | **Durchschnittliche E/A-Größe des Quelldatenträgers** |**Durchschnittliche Datenänderungsrate des Quelldatenträgers** | **Gesamte Datenänderungsrate des Quelldatenträgers pro Tag**
---|---|---|---
Standardspeicher | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 8 KB    | 2 MB/s | 168 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 16 KB | 4 MB/s |    336 GB pro Datenträger
Premium-Datenträger – P10 oder P15 | 32 KB oder höher | 8 MB/s | 672 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 8 KB    | 5 MB/s | 421 GB pro Datenträger
Premium-Datenträger – P20, P30, P40 oder P50 | 16 KB oder höher |20 MB/s | 1\.684 GB pro Datenträger

## <a name="replicated-machines---networking"></a>Replizierte Computer – Netzwerk
**Einstellung** | **Unterstützung** | **Details**
--- | --- | ---
NIC | Unterstützte maximale Anzahl für eine bestimmte Azure-VM-Größe | Netzwerkkarten werden erstellt, wenn die VM während des Failovers erstellt wird.<br/><br/> Die Anzahl von Netzwerkkarten auf dem virtuellen Failovercomputer ist abhängig von der Anzahl von Netzwerkkarten, die auf dem virtuellen Quellcomputer vorhanden waren, als die Replikation aktiviert wurde. Falls Sie eine Netzwerkkarte nach dem Aktivieren der Replikation hinzufügen oder entfernen, wirkt sich dies nicht auf die Anzahl von Netzwerkkarten auf der replizierten VM nach dem Failover aus. <br/><br/> Nach einem Failover entspricht die Reihenfolge der Netzwerkadapter unter Umständen nicht mehr der ursprünglichen Reihenfolge. <br/><br/> Sie können die Netzwerkadapter in der Zielregion gemäß den Benennungskonventionen Ihrer Organisation umbenennen. Die Umbenennung von Netzwerkadaptern wird mit PowerShell unterstützt.
Internetlastenausgleich | Nicht unterstützt | Sie können einen öffentlichen/Internet-Lastenausgleich in der primären Region einrichten. Öffentliche/Internet-Lastenausgleiche werden jedoch von Azure Site Recovery in der DR-Region nicht unterstützt.
Interner Lastenausgleich | Unterstützt | Ordnen Sie den vorkonfigurierten Lastenausgleich mit einem Azure-Automatisierungsskript in einem Wiederherstellungsplan zu.
Öffentliche IP-Adresse | Unterstützt | Ordnen Sie der Netzwerkkarte eine vorhandene öffentliche IP-Adresse zu. Oder erstellen Sie eine öffentliche IP-Adresse, und ordnen Sie diese der Netzwerkkarte zu, indem Sie ein Azure-Automatisierungsskript in einem Wiederherstellungsplan verwenden.
NSG auf Netzwerkkarte | Unterstützt | Ordnen Sie die NSG der Netzwerkkarte zu, indem Sie ein Azure-Automatisierungsskript in einem Wiederherstellungsplan verwenden.
NSG im Subnetz | Unterstützt | Ordnen Sie die NSG dem Subnetz zu, indem Sie ein Azure-Automatisierungsskript in einem Wiederherstellungsplan verwenden.
Reservierte (statische) IP-Adresse | Unterstützt | Wenn die Netzwerkkarte auf dem virtuellen Quellcomputer über eine statische IP-Adresse verfügt und für das Zielsubnetz die gleiche IP-Adresse verfügbar ist, wird sie der VM zugewiesen, für die das Failover ausgeführt wurde.<br/><br/> Falls das Zielsubnetz nicht über die gleiche IP-Adresse verfügt, wird eine der verfügbaren IP-Adressen im Subnetz für den virtuellen Computer reserviert.<br/><br/> Sie können eine feste IP-Adresse und ein Subnetz auch unter **Replizierte Elemente** > **Einstellungen** > **Compute und Netzwerk** > **Netzwerkschnittstellen** angeben.
Dynamische IP-Adresse | Unterstützt | Wenn die Netzwerkkarte auf der Quelle über eine dynamische IP-Adressierung verfügt, ist die Netzwerkkarte auf dem virtuellen Computer, für den das Failover ausgeführt wurde, standardmäßig ebenfalls dynamisch.<br/><br/> Sie können dies in eine feste IP-Adresse ändern, falls es erforderlich ist.
Mehrere IP-Adressen | Nicht unterstützt | Wenn Sie ein Failover für einen virtuellen Computer mit einem Netzwerkadapter mit mehreren IP-Adressen ausführen, wird nur die primäre IP-Adresse des Netzwerkadapters in der Quellregion beibehalten. Wenn Sie mehrere IP-Adressen zuweisen möchten, können Sie virtuelle Computer zu einem [Wiederherstellungsplan](recovery-plan-overview.md) hinzufügen und ein Skript anfügen, um dem Plan zusätzliche IP-Adressen zuzuweisen. Alternativ können Sie die Änderung nach dem Failover manuell oder mithilfe eines Skripts vornehmen.
Traffic Manager     | Unterstützt | Sie können Traffic Manager so vorkonfigurieren, dass Datenverkehr in regelmäßigen Abständen an den Endpunkt in der Quellregion und bei einem Failover an den Endpunkt in der Zielregion weitergeleitet wird.
Azure DNS | Unterstützt |
Benutzerdefinierter DNS    | Unterstützt |
Nicht authentifizierter Proxy | Unterstützt | [Weitere Informationen](./azure-to-azure-about-networking.md)
Authentifizierter Proxy | Nicht unterstützt | Wenn der virtuelle Computer einen authentifizierten Proxy für ausgehende Verbindungen verwendet, kann er nicht mit Azure Site Recovery repliziert werden.
VPN-Site-to-Site-Verbindung mit lokalem Standort<br/><br/>(mit oder ohne ExpressRoute)| Unterstützt | Stellen Sie sicher, dass die UDRs und NSGs so konfiguriert sind, dass der Datenverkehr für die Sitewiederherstellung nicht lokal weitergeleitet wird. [Weitere Informationen](./azure-to-azure-about-networking.md)
VNet-zu-VNet-Verbindung    | Unterstützt | [Weitere Informationen](./azure-to-azure-about-networking.md)
VNET-Dienstendpunkte | Unterstützt | Wenn Sie den Zugriff auf virtuelle Netzwerke in Speicherkonten einschränken, stellen Sie sicher, dass den vertrauenswürdigen Microsoft-Diensten Zugriff auf das Speicherkonto gewährt wird.
Beschleunigte Netzwerke | Unterstützt | Auf dem virtuellen Quellcomputer muss der beschleunigte Netzwerkbetrieb aktiviert sein. [Weitere Informationen](azure-vm-disaster-recovery-with-accelerated-networking.md)
Palo Alto Network Appliance | Nicht unterstützt | Bei Appliances von Drittanbietern gibt es häufig Beschränkungen, die vom Anbieter innerhalb des virtuellen Computers auferlegt werden. Azure Site Recovery erfordert die Verfügbarkeit eines Agents sowie von Erweiterungen und ausgehender Konnektivität. Die Appliance lässt jedoch nicht zu, dass ausgehende Aktivitäten innerhalb des virtuellen Computers konfiguriert werden.
IPv6  | Nicht unterstützt | Gemischte Konfigurationen, die sowohl IPv4 als auch IPv6 einschließen, werden ebenfalls nicht unterstützt. Entfernen Sie den IPv6-Bereich aus dem Subnetz, bevor Sie Site Recovery-Vorgänge ausführen.
Private Link-Zugriff auf den Site Recovery-Dienst | Unterstützt | [Weitere Informationen](azure-to-azure-how-to-enable-replication-private-endpoints.md)



## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Netzwerkkonzepte für die Replikation zwischen Azure-Standorten](./azure-to-azure-about-networking.md) zum Replizieren von virtuellen Azure-Computern.
- Stellen Sie die Notfallwiederherstellung bereit, indem Sie [Azure-VMs replizieren](./azure-to-azure-quickstart.md).
