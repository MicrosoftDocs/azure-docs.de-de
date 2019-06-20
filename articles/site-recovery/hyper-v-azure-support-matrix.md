---
title: Unterstützungsmatrix für die Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure | Microsoft-Dokumentation
description: Dieser Artikel fasst die unterstützten Komponenten und Anforderungen für die Hyper-V-Notfallwiederherstellung in Azure mit Azure Site Recovery zusammen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: bce9f3b3a574d27e2fb47fb9b2da9470c43fd2eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399428"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Unterstützungsmatrix für die Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure


In diesem Artikel werden die unterstützten Komponenten und Einstellungen für die Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure mit [Azure Site Recovery](site-recovery-overview.md) beschrieben.


## <a name="supported-scenarios"></a>Unterstützte Szenarien

**Szenario** | **Details**
--- | ---
Hyper-V mit Virtual Machine Manager | Sie können die Notfallwiederherstellung in Azure für virtuelle Computer auf Hyper-V-Hosts durchführen, die im System Center Virtual Machine Manager-Fabric verwaltet werden.<br/><br/> Dieses Szenario können Sie über das Azure-Portal oder mit PowerShell bereitstellen.<br/><br/> Wenn Hyper-V-Hosts mit Virtual Machine Manager verwaltet werden, können Sie auch die Notfallwiederherstellung an einem sekundären lokalen Standort durchführen. Weitere Informationen zu diesem Szenario finden Sie in [diesem Tutorial](hyper-v-vmm-disaster-recovery.md).
Hyper-V ohne Virtual Machine Manager | Sie können die Notfallwiederherstellung in Azure für virtuelle Computer auf Hyper-V-Hosts ausführen, die nicht von Virtual Machine Manager verwaltet werden.<br/><br/> Dieses Szenario können Sie über das Azure-Portal oder mit PowerShell bereitstellen.


## <a name="on-premises-servers"></a>Lokale Server

**Server** | **Anforderungen** | **Details**
--- | --- | ---
Hyper-V (Ausführung ohne Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (einschließlich Server Core-Installation), Windows Server 2012 R2 mit den neuesten Updates | Wenn Sie Windows Server 2012 R2 mit SCVMM 2012 R2 mit Azure Site Recovery bzw. nur SCVMM 2012 R2 mit Azure Site Recovery konfiguriert haben und nun ein Upgrade des Betriebssystems durchführen möchten, befolgen Sie die Anweisungen in der [Dokumentation](upgrade-2012R2-to-2016.md). 
Hyper-V (Ausführung mit Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Wenn Virtual Machine Manager verwendet wird, sollten Windows Server 2019-Hosts in Virtual Machine Manager 2019 verwaltet werden. Ebenso sollten Windows Server 2016-Hosts in Virtual Machine Manager 2016 verwaltet werden.<br/><br/>


## <a name="replicated-vms"></a>Replizierte VMs


In der folgenden Tabelle wird die VM-Unterstützung zusammengefasst. Site Recovery unterstützt alle Workloads, die auf einem unterstützten Betriebssystem ausgeführt werden.

 **Komponente** | **Details**
--- | ---
Konfiguration des virtuellen Computers | Virtuelle Computer, die nach Azure repliziert werden sollen, müssen die [Azure-Anforderungen](#azure-vm-requirements) erfüllen.
Gastbetriebssystem | Alle [für Azure unterstützten](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases) Gastbetriebssysteme.<br/><br/> Windows Server 2016 Nano Server wird nicht unterstützt.


## <a name="vmdisk-management"></a>VM-/Datenträgerverwaltung

**Aktion** | **Details**
--- | ---
Größe des Datenträgers auf einer replizierten Hyper-V-VM ändern | Nicht unterstützt. Deaktivieren Sie die Replikation, nehmen Sie die Änderung vor, und aktivieren Sie anschließend erneut die Replikation für die VM.
Datenträger auf einer replizierten Hyper-V-VM hinzufügen | Nicht unterstützt. Deaktivieren Sie die Replikation, nehmen Sie die Änderung vor, und aktivieren Sie anschließend erneut die Replikation für die VM.

## <a name="hyper-v-network-configuration"></a>Hyper-V-Netzwerkkonfiguration

**Komponente** | **Hyper-V mit Virtual Machine Manager** | **Hyper-V ohne Virtual Machine Manager**
--- | --- | ---
Hostnetzwerk: NIC Teaming | Ja | Ja
Hostnetzwerk: VLAN | Ja | Ja
Hostnetzwerk: IPv4 | Ja | Ja
Hostnetzwerk: IPv6 | Nein | Nein
Gast-VM-Netzwerk: NIC Teaming | Nein | Nein
Gast-VM-Netzwerk: IPv4 | Ja | Ja
Gast-VM-Netzwerk: IPv6 | Nein | Ja
Gast-VM-Netzwerk: Statische IP-Adresse (Windows) | Ja | Ja
Gast-VM-Netzwerk: Statische IP-Adresse (Linux) | Nein | Nein
Gast-VM-Netzwerk: Multi-NIC | Ja | Ja



## <a name="azure-vm-network-configuration-after-failover"></a>Netzwerkkonfiguration für virtuellen Azure-Computer (nach Failover)

**Komponente** | **Hyper-V mit Virtual Machine Manager** | **Hyper-V ohne Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Reservierte IP | Ja | Ja
IPv4 | Ja | Ja
Quell-IP-Adresse beibehalten | Ja | Ja
Azure Virtual Network-Dienstendpunkte<br/> (ohne Azure Storage-Firewalls) | Ja | Ja
Beschleunigter Netzwerkbetrieb | Nein | Nein


## <a name="hyper-v-host-storage"></a>Hyper-V-Hostspeicher

**Speicher** | **Hyper-V mit Virtual Machine Manager** | **Hyper-V ohne Virtual Machine Manager**
--- | --- | --- 
NFS | Nicht verfügbar | Nicht verfügbar
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Multipfad (MPIO). Getestet mit:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM für CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V-VM-Gastspeicher

**Speicher** | **Hyper-V mit Virtual Machine Manager** | **Hyper-V ohne Virtual Machine Manager**
--- | --- | ---
VMDK | Nicht verfügbar | Nicht verfügbar
VHD/VHDX | Ja | Ja
VM der 2. Generation | Ja | Ja
EFI/UEFI| Ja | Ja
Freigegebener Clusterdatenträger | Nein | Nein
Verschlüsselter Datenträger | Nein | Nein
NFS | Nicht verfügbar | Nicht verfügbar
SMB 3.0 | Nein | Nein
RDM | Nicht verfügbar | Nicht verfügbar
Datenträger > 1 TB | Ja, bis zu 4.095 GB | Ja, bis zu 4.095 GB
Datenträger: 4K für logischen und physischen Sektor | Nicht unterstützt: Gen 1, Gen 2 | Nicht unterstützt: Gen 1, Gen 2
Datenträger: 4K für logischen, 512 Byte für physischen Sektor | Ja |  Ja
Logische Volumeverwaltung (Logical Volume Management, LVM). LVM wird nur für Datenträger unterstützt. Azure stellt nur einen einzelnen Betriebssystemdatenträger bereit. | Ja | Ja
Volume mit Stripesetdatenträger > 1 TB | Ja | Ja
Speicherplätze | Ja | Ja
Datenträger laufendem Systembetrieb hinzufügen/entfernen | Nein | Nein
Ausschließen von Datenträgern | Ja | Ja
Multipfad (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Komponente** | **Hyper-V mit Virtual Machine Manager** | **Hyper-V ohne Virtual Machine Manager**
--- | --- | ---
Lokal redundanter Speicher | Ja | Ja
Georedundanter Speicher | Ja | Ja
Georedundanter Speicher mit Lesezugriff | Ja | Ja
Speicherebene „Kalt“ | Nein | Nein
Speicherebene „Heiß“| Nein | Nein
Blockblobs | Nein | Nein
Verschlüsselung ruhender Daten (SSE)| Ja | Ja
Storage Premium | Ja | Ja
Import-/Exportdienst | Nein | Nein
Azure Storage-Firewalls für virtuelle Netzwerke, konfiguriert im Zielspeicher-/Cachespeicherkonto (zum Speichern von Replikationsdaten) | Nein | Nein


## <a name="azure-compute-features"></a>Azure-Computefeatures

**Feature** | **Hyper-V mit Virtual Machine Manager** | **Hyper-V ohne Virtual Machine Manager**
--- | --- | ---
Verfügbarkeitsgruppen | Ja | Ja
HUB | Ja | Ja  
Verwaltete Datenträger | Ja, für Failover.<br/><br/> Failback von verwalteten Datenträgern wird nicht unterstützt. | Ja, für Failover.<br/><br/> Failback von verwalteten Datenträgern wird nicht unterstützt.

## <a name="azure-vm-requirements"></a>Azure-VM-Anforderungen

Lokale virtuelle Computer, die Sie in Azure replizieren, müssen die in dieser Tabelle zusammengefassten Azure-VM-Anforderungen erfüllen.

**Komponente** | **Anforderungen** | **Details**
--- | --- | ---
Gastbetriebssystem | Site Recovery unterstützt alle Betriebssysteme, die [von Azure unterstützt werden](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Architektur des Gastbetriebssystems | 64 Bit | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Bis zu 2.048 GB für VMs der 1. Generation.<br/><br/> Bis zu 300 GB für VMs der 2. Generation.  | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Datenträgern für Daten | Maximal 16  | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des VHD-Datenträgers | Bis zu 4.095 GB | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Freigegebene VHD | Nicht unterstützt | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Nicht unterstützt | Beim Überprüfen der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Festplattenformat | VHD <br/><br/> VHDX | Site Recovery konvertiert VHDX automatisch in VHD, wenn Sie das Failover auf Azure anstoßen. Wenn Sie ein Failback zum lokalen Speicher durchführen, wird für die virtuellen Computer weiterhin das VHDX-Format verwendet.
BitLocker | Nicht unterstützt | BitLocker muss deaktiviert sein, bevor Sie die Replikation für einen virtuellen Computer aktivieren.
Name des virtuellen Computers | Zwischen 1 und 63 Zeichen. Ist auf Buchstaben, Zahlen und Bindestriche beschränkt. Der VM-Name muss mit einem Buchstaben oder einer Zahl beginnen und enden. | Aktualisieren Sie den Wert in den VM-Eigenschaften in Site Recovery.
VM-Typ | Generation 1<br/><br/> Generation 2 – Windows | Zwei virtuelle Computer der Generation 2 mit einem Betriebssystem-Datenträger des Typs „Basic“ (mit einem oder zwei als VHDX formatierten Datenvolumes) und weniger als 300 GB Speicherplatz werden unterstützt.<br></br>Linux-VMs der Generation 2 werden nicht unterstützt. [Weitere Informationen](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Aktionen mit dem Recovery Services-Tresor

**Aktion** |  **Hyper-V mit Virtual Machine Manager** | **Hyper-V ohne Virtual Machine Manager**
--- | --- | ---
Tresor über Ressourcengruppen hinweg verschieben<br/><br/> Innerhalb von und über Abonnements hinweg | Nein | Nein
Speicher, Netzwerk, Azure-VMs über Ressourcengruppen hinweg verschieben<br/><br/> Innerhalb von und über Abonnements hinweg | Nein | Nein

> [!NOTE]
> Wenn Hyper-VMs (mit oder ohne Verwaltung durch SCVMM) von lokalen VMs in Azure repliziert werden, kann die Replikation aus einer bestimmten Umgebung heraus, nämlich dem Hyper-V-Standort oder SCVMM (je nachdem, was davon zutrifft), nur in einem bestimmten AD-Mandanten durchgeführt werden.


## <a name="provider-and-agent"></a>Anbieter und Agent

Damit Ihre Bereitstellung mit den Einstellungen in diesem Artikel kompatibel ist, stellen Sie sicher, dass Sie die neuesten Anbieter- und Agent-Versionen ausführen.

**Name** | **Beschreibung** | **Details**
--- | --- | --- 
Azure Site Recovery-Anbieter | Koordiniert die Kommunikation zwischen lokalen Servern und Azure <br/><br/> Hyper-V mit Virtual Machine Manager: Installiert auf Virtual Machine Manager-Servern<br/><br/> Hyper-V ohne Virtual Machine Manager: Installiert auf Hyper-V-Hosts| Aktuelle Version: 5.1.2700.1 (über das Azure-Portal verfügbar)<br/><br/> [Neueste Features und Fixes](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-Agent | Koordiniert die Replikation zwischen Hyper-V-VMs und Azure<br/><br/> Installation auf lokalen Hyper-V-Servern (mit oder ohne Virtual Machine Manager) | Aktueller Agent über das Portal verfügbar






## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie Azure für die Notfallwiederherstellung von lokalen Hyper-V-VMs [vorbereiten](tutorial-prepare-azure.md).
