---
title: Unterstützung der Migration physischer Server in Azure Migrate
description: Hier finden Sie Informationen zur Unterstützung der Migration physischer Server in Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: c549cae0b7bdc5e237ec3cf704a1ead53db91449
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754027"
---
# <a name="support-matrix-for-physical-server-migration"></a>Unterstützungsmatrix für die Migration physischer Server

Dieser Artikel enthält eine Zusammenfassung der Unterstützungseinstellungen und Einschränkungen bei der Migration physischer Server mit [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) (Azure Migrate: Servermigration). Informationen zur Bewertung physischer Server für die Migration zu Azure finden Sie in der [Unterstützungsmatrix für die Bewertung physischer Server](migrate-support-matrix-physical.md).

## <a name="migrating-machines-as-physical"></a>Migrieren von Computern als physische Computer

Lokale Computer können mithilfe der Agent-basierten Replikation als physische Server migriert werden. Mit diesem Tool können Sie viele verschiedene Computer zu Azure migrieren:

- Lokale physische Server
- Virtuelle Computer, die über eine Plattform wie Xen oder KVM virtualisiert werden
- Virtuelle Hyper-V- oder VMware-Computer, falls Sie aus irgendeinem Grund nicht die Standardvorgehensweise für [Hyper-V](tutorial-migrate-hyper-v.md) oder [VMware](server-migrate-overview.md) verwenden möchten
- Virtuelle Computer, die in privaten Clouds ausgeführt werden
- Virtuelle Computer, die in öffentlichen Clouds wie Amazon Web Services (AWS) oder Google Cloud Platform (GCP) ausgeführt werden


## <a name="migration-limitations"></a>Einschränkungen bei der Migration

Für die Replikation können bis zu zehn virtuelle Computer gleichzeitig ausgewählt werden. Wenn Sie weitere Computer migrieren möchten, replizieren Sie sie in Gruppen von 10.


## <a name="physical-server-requirements"></a>Anforderungen für physische Server

Die Tabelle enthält eine Übersicht über die Unterstützung für physische Server, die mit der Agent-basierten Migration migriert werden sollen.

**Unterstützung** | **Details**
--- | ---
**Computerworkload** | Azure Migrate unterstützt die Migration beliebiger Workloads (u. a. Active Directory- und SQL Server-Workloads), die auf einem unterstützten Computer ausgeführt werden.
**Betriebssysteme** | Die neuesten Informationen finden Sie im Abschnitt zur [Betriebssystemunterstützung](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) für Site Recovery. Azure Migrate bietet identische Betriebssystemunterstützung.
**Linux-Dateisystem/-Gastspeicher** | Die neuesten Informationen finden Sie im Abschnitt zur [Linux-Dateisystemunterstützung](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) für Site Recovery. Azure Migrate bietet identische Linux-Dateisystemunterstützung.
**Netzwerk/Speicher** | Die neuesten Informationen finden Sie in den Bedingungen für [Netzwerk](../site-recovery/vmware-physical-azure-support-matrix.md#network) und [Speicher](../site-recovery/vmware-physical-azure-support-matrix.md#storage) für Site Recovery. Für Azure Migrate gelten die gleichen Netzwerk-/Speicheranforderungen.
**Anforderungen für Azure** | Die neuesten Informationen finden Sie in den Bedingungen für [Azure-Netzwerk](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [Speicher](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage) und [Compute](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) für Site Recovery. Bei Azure Migrate gelten die gleichen Anforderungen für die Migration physischer Server.
**Mobilitätsdienst** | Der Mobilitätsdienst-Agent muss auf jedem Computer installiert sein, den Sie migrieren möchten.
**UEFI-Start** | Unterstützt. UEFI-basierte Computer werden zu virtuellen Azure-Computern der 2. Generation migriert.  <br/><br/> Der Betriebssystemdatenträger sollte bis zu vier Partitionen aufweisen, und Volumes sollten mit NTFS formatiert sein.
**UEFI: Sicherer Start**         | Nicht unterstützt für die Migration.
**Zieldatenträger** | Computer können nur zu verwalteten Datenträgern (HDD Standard, SSD Standard, SSD Premium) in Azure migriert werden.
**Datenträgergröße** | 2 TB für den Betriebssystemdatenträger (BIOS-Start); 4 TB für den Betriebssystemdatenträger (UEFI-Start); 8 TB für Datenträger
**Einschränkungen für Datenträger** |  Bis zu 63 Datenträger pro Computer
**Verschlüsselte Datenträger/Volumes** |  Die Migration von Computern mit verschlüsselten Datenträgern/Volumes wird nicht unterstützt.
**Freigegebener Datenträgercluster** | Wird nicht unterstützt.
**Unabhängige Datenträger** | Unterstützt.
**Pass-Through-Datenträger** | Unterstützt.
**NFS** | NFS-Volumes, die auf den Computern als Volumes eingebunden sind, werden nicht repliziert.
**iSCSI-Ziele** | Computer mit iSCSI-Zielen werden bei der Migration ohne Agent nicht unterstützt.
**Multipfad-E/A** | Wird nicht unterstützt.
**Storage vMotion** | Unterstützt
**Kombinierte Netzwerkadapter** | Wird nicht unterstützt.
**IPv6** | Wird nicht unterstützt.



## <a name="replication-appliance-requirements"></a>Anforderungen der Replikationsappliance

Stellen Sie bei der manuellen Einrichtung der Replikationsappliance auf einem physischen Server sicher, dass sie die in der Tabelle angegebenen Anforderungen erfüllt. Wenn Sie die Azure Migrate-Replikationsappliance mit der OVA-Vorlage im Azure Migrate-Hub als virtuellen VMware-Computer einrichten, wird die Appliance mit Windows Server 2016 eingerichtet und erfüllt die Supportanforderungen. 

- Informationen zu den Anforderungen für die Replikationsappliance finden Sie [hier](migrate-replication-appliance.md#appliance-requirements).
- MySQL muss auf der Appliance installiert sein. Erfahren Sie etwas über die [Installationsoptionen ](migrate-replication-appliance.md#mysql-installation).
- Erfahren Sie etwas über die [URLs](migrate-replication-appliance.md#url-access), auf die die Replikationsappliance Zugriff benötigt.

## <a name="azure-vm-requirements"></a>Azure-VM-Anforderungen

Alle lokalen VMs, die in Azure repliziert werden, müssen die in dieser Tabelle zusammengefassten Azure-VM-Anforderungen erfüllen. Wenn Site Recovery eine Voraussetzungsprüfung für die Replikation durchführt, ist diese nicht erfolgreich, wenn einige der Anforderungen nicht erfüllt sind.

**Komponente** | **Anforderungen** | **Details**
--- | --- | ---
Gastbetriebssystem | Überprüft die unterstützten Betriebssysteme.<br/> Sie können alle Workloads migrieren, die unter einem unterstützten Betriebssystem ausgeführt werden. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Architektur des Gastbetriebssystems | 64 Bit. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Bis zu 2.048 GB. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Datenträgern für Daten | Maximal 64. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Datenträgergröße | Bis zu 4.095 GB | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Freigegebene VHD | Wird nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Wird nicht unterstützt. | Beim Überprüfen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
BitLocker | Wird nicht unterstützt. | BitLocker muss deaktiviert sein, bevor Sie die Replikation für einen Computer aktivieren.
Name des virtuellen Computers | 1 bis 63 Zeichen.<br/> Ist auf Buchstaben, Zahlen und Bindestriche beschränkt.<br/><br/> Der Computername muss mit einem Buchstaben oder einer Ziffer beginnen und enden. |  Aktualisieren Sie den Wert in den Computereigenschaften in Site Recovery.
Herstellen einer Verbindung nach der Migration: Windows | So stellen Sie nach der Migration eine Verbindung mit Azure-VMs unter Windows her:<br/> – Aktivieren Sie vor der Migration RDP auf dem lokalen virtuellen Computer. Stellen Sie sicher, dass TCP- und UDP-Regeln für das Profil **Öffentlich** hinzugefügt werden und dass RDP unter **Windows-Firewall** > **Zugelassene Apps** für alle Profile zugelassen ist.<br/> Aktivieren Sie bei Site-to-Site-VPN-Zugriff das RDP unter **Windows-Firewall** -> **Zugelassene Apps und Features** für **private Netzwerke und Domänennetzwerke**. Achten Sie außerdem darauf, dass die SAN-Richtlinie des Betriebssystems auf **OnlineAll** festgelegt ist. [Weitere Informationen](prepare-for-migration.md) |
Herstellen einer Verbindung nach der Migration: Linux | So stellen Sie nach der Migration per SSH eine Verbindung mit Azure-VMs her:<br/> Überprüfen Sie vor der Migration auf dem lokalen Computer, ob der Secure Shell-Dienst auf „Start“ festgelegt ist und ob die Firewallregeln eine SSH-Verbindung zulassen.<br/> Lassen Sie nach dem Failover auf der Azure-VM eingehende Verbindungen am SSH-Port für die Netzwerksicherheitsgruppen-Regeln auf der VM, für die ein Failover ausgeführt wurde, sowie für das Azure-Subnetz zu, mit dem die VM verbunden ist. Fügen Sie außerdem der VM eine öffentliche IP-Adresse hinzu. |  


## <a name="next-steps"></a>Nächste Schritte

[Migrieren](tutorial-migrate-physical-virtual-machines.md) physischer Server
