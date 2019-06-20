---
title: Unterstützungsmatrix für die Notfallwiederherstellung von Hyper-V-VMs in VMM-Clouds in einem sekundären Standort mit Azure Site Recovery | Microsoft-Dokumentation
description: Fasst Informationen zur Unterstützung für die Replikation von virtuellen Hyper-V-Computern in VMM-Clouds an einen sekundären Standort mit Azure Site Recovery zusammen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: e8b8f9856fe7e0fa591ceb42aab97e92642b6098
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399353"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Unterstützungsmatrix für die Notfallwiederherstellung von Hyper-V-VMs in einem sekundären Standort

In diesem Artikel wird zusammengefasst, was unterstützt wird, wenn Sie den [Azure Site Recovery](site-recovery-overview.md)-Dienst nutzen, um virtuelle Hyper-V-Computer, die in System Center Virtual Machine Manager-Clouds (VMM) verwaltet werden, an sekundäre Standorte zu replizieren. Wenn Sie virtuelle Hyper-V-Computer in Azure replizieren möchten, lesen Sie [diese Unterstützungsmatrix](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Eine Replikation an einen sekundären Standort ist nur möglich, wenn die Hyper-V-Hosts in VMM-Clouds verwaltet werden.

  

## <a name="host-servers"></a>Hostserver

**Betriebssystem** | **Details**
--- | ---
Windows Server 2012 R2 | Auf Servern müssen die neuesten Updates ausgeführt werden.
Windows Server 2016 |  VMM 2016-Clouds mit einer Mischung aus Windows Server 2016- und Windows Server 2012 R2-Hosts werden derzeit nicht unterstützt.<br/><br/> Bereitstellungen, die von System Center 2012 R2 VMM 2012 R2 auf System Center 2016 aktualisiert wurden, werden derzeit nicht unterstützt.


## <a name="replicated-vm-support"></a>Unterstützung replizierter VMs

In der folgenden Tabelle werden die unterstützten Betriebssysteme für mit Site Recovery replizierte Computer aufgeführt. Unter dem unterstützten Betriebssystem können beliebige Workloads ausgeführt werden.

**Windows-Version** | **Hyper-V (mit VMM)**
--- | ---
Windows Server 2016 | Alle [von Hyper-V unterstützten](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) Gastbetriebssysteme unter Windows Server 2016 
Windows Server 2012 R2 | Alle [von Hyper-V unterstützten](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) Gastbetriebssysteme unter Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Speicher eines Linux-Computers

Nur Linux-Computer mit dem folgenden Speicher können repliziert werden:

- Dateisystem (EXT3, ETX4, ReiserFS, XFS)
- Multipfadsoftware (Geräte-Mapper)
- Volume-Manager (LVM2)
- Physische Server mit HP CCISS-Controllerspeicher werden nicht unterstützt.
- Das ReiserFS-Dateisystem wird nur unter SUSE Linux Enterprise Server 11 SP3 unterstützt.

## <a name="network-configuration---hostguest-vm"></a>Netzwerkkonfiguration – Host/Gast-VM

**Konfiguration** | **Unterstützt**  
--- | --- 
Host – NIC-Teamvorgang | Ja 
Host – VLAN | Ja 
Host – IPv4 | Ja 
Host – IPv6 | Nein 
Gast-VM – NIC-Teamvorgang | Nein
Gast-VM – IPv4 | Ja
Gast-VM – IPv6 | Nein
Gast-VM – Windows/Linux – Statische IP-Adresse | Ja
Gast-VM – Multi-NIC | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Hostspeicher

**Speicher (Host)** | **Unterstützt**
--- | --- 
NFS | –
SMB 3.0 |  Ja
SAN (ISCSI) | Ja
Multipfad (MPIO) | Ja

### <a name="guest-or-physical-server-storage"></a>Gast- oder physischer Serverspeicher

**Konfiguration** | **Unterstützt**
--- | --- | 
VMDK |  –
VHD/VHDX | Ja (bis zu 16 Datenträger)
Gen 2-VM | Ja
Freigegebener Clusterdatenträger | Nein
Verschlüsselter Datenträger | Nein
UEFI| –
NFS | Nein
SMB 3.0 | Nein
RDM | –
Datenträger > 1 TB | Ja
Volume mit Stripesetdatenträgern > 1 TB<br/><br/> LVM | Ja
Speicherplätze | Ja
Datenträger laufendem Systembetrieb hinzufügen/entfernen | Nein
Ausschließen von Datenträgern | Ja
Multipfad (MPIO) | Ja

## <a name="vaults"></a>Tresore

**Aktion** | **Unterstützt**
--- | --- 
Verschieben von Tresoren zwischen Ressourcengruppen (innerhalb oder zwischen Abonnements) |  Nein
Verschieben von Speicher, Netzwerk, Azure-VMs zwischen Ressourcengruppen (innerhalb oder zwischen Abonnements) | Nein

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery-Anbieter

Der Anbieter koordiniert die Kommunikation zwischen VMM-Servern. 

**Neueste** | **Updates**
--- | --- 
5.1.19 ([über das Portal verfügbar](https://aka.ms/downloaddra)) | [Neueste Features und Fixes](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Nächste Schritte

[Replizieren von Hyper-V-VMs in VMM-Clouds in einer sekundären Cloud](tutorial-vmm-to-vmm.md)

