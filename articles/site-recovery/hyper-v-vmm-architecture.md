---
title: Architektur für die Hyper-V-Notfallwiederherstellung in einem sekundären lokalen Standort mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel bietet einen Überblick über die Architektur für die Notfallwiederherstellung lokaler Hyper-V-VMs in einem sekundären System Center-VMM-Standort mit Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 22f21f11b0c374724bc6924f30ea20a21de6ab90
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398171"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektur – Hyper-V-Replikation an einem sekundären Standort

Dieser Artikel beschreibt die Komponenten und Prozesse bei der Replikation lokaler virtueller Hyper-V-Computer (Virtual Machines, VMs) in System Center-VMM-Clouds (Virtual Machine Manager) an einem sekundären VMM-Standort mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) über das Azure-Portal.


## <a name="architectural-components"></a>Komponenten der Architektur

Die folgende Tabelle und Grafik bietet eine Übersicht der Komponenten, die für die Hyper-V-Replikation an einem sekundären Standort verwendet werden.

**Komponente** | **Anforderung** | **Details**
--- | --- | ---
**Azure** | Azure-Abonnement | Sie erstellen einen Recovery Services-Tresor im Azure-Abonnement, um die Replikation zwischen VMM-Standorten zu orchestrieren und zu verwalten.
**VMM-Server** | Sie benötigen einen primären und sekundären VMM-Standort. | Wir empfehlen Ihnen die Verwendung eines VMM-Servers am primären Standort und eines VMM-Servers am sekundären Standort.
**Hyper-V-Server** |  Mindestens ein Hyper-V-Hostserver in der primären und der sekundären VMM-Cloud. | Die Daten werden zwischen dem primären und sekundären Hyper-V-Hostserver über das LAN oder VPN per Kerberos- oder Zertifikatauthentifizierung repliziert.  
**Virtuelle Hyper-V-Computer** | Auf dem Hyper-V-Hostserver. | Der Quellhostserver muss über mindestens einen virtuellen Computer verfügen, den Sie replizieren möchten.

**Lokal-zu-lokal-Architektur**

![Lokal zu lokal](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replikationsprozess

1. Wenn die erste Replikation ausgelöst wird, wird eine [Momentaufnahem der Hyper-V-VM](https://technet.microsoft.com/library/dd560637.aspx) erstellt.
2. Virtuelle Festplatten auf der VM werden nacheinander an den sekundären Standort repliziert.
3. Falls während der ersten Replikation Datenträgeränderungen auftreten, werden die Änderungen mit dem Replication Tracker für Hyper-V-Replikate in Form von Hyper-V-Replikationsprotokollen (.hrl) nachverfolgt. Diese Protokolldateien befinden sich im gleichen Ordner wie die Datenträger. Jeder Datenträger verfügt über eine zugeordnete HRL-Datei, die an den sekundären Speicher gesendet wird. Beachten Sie, dass die Momentaufnahme- und Protokolldateien Festplattenressourcen belegen, während die anfängliche Replikation durchgeführt wird.
4. Nach Abschluss der ersten Replikation wird die Momentaufnahme des virtuellen Computers gelöscht, und die Deltareplikation beginnt.
5. Die im Protokoll erfassten Datenträgeränderungen werden synchronisiert und mit dem übergeordneten Datenträger zusammengeführt.


## <a name="failover-and-failback-process"></a>Failover- und Failbackprozesse

- Sie können ein Failover für einen einzelnen Computer ausführen oder Wiederherstellungspläne erstellen, um das Failover von mehreren Computern zu orchestrieren.
- Sie können ein geplantes oder ungeplantes Failover zwischen lokalen Standorten ausführen. Wenn Sie ein geplantes Failover durchführen, werden die Quell-VMs heruntergefahren, um sicherzustellen, dass kein Datenverlust auftritt.
    - Nach einem nicht geplanten Failover zu einem sekundären Standort sind die Computer am sekundären Standort nicht geschützt.
    - Nach einem geplanten Failover sind die Computer am sekundären Standort geschützt.
- Nachdem das erste Failover ausgeführt wird, committen Sie es, um von der Replikat-VM auf die Workload zuzugreifen.
- Sie können ein Failback ausführen, wenn der primäre Standort wieder verfügbar ist.
    - Leiten Sie eine umgekehrte Replikation ein, um die Replikation vom sekundären an den primären Standort zu starten. Die umgekehrte Replikation versetzt die virtuellen Computer in einen geschützten Zustand, aber das sekundäre Rechenzentrum bleibt weiterhin aktiv.
    - Initiieren Sie ein geplantes Failover vom sekundären zum primären Standort, und führen Sie anschließend eine erneute umgekehrte Replikation durch, um den primären Standort wieder zum aktiven Standort zu machen.



## <a name="next-steps"></a>Nächste Schritte


Absolvieren Sie [dieses Tutorial](hyper-v-vmm-disaster-recovery.md), um die Hyper-V-Replikation zwischen VMM-Clouds zu aktivieren.
