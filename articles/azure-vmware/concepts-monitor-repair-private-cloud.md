---
title: 'Konzepte: Überwachen und Reparieren von privaten Azure VMware Solution-Clouds'
description: Es wird beschrieben, wie VMware ESXi-Server in Azure VMware Solution in einer privaten Azure VMware Solution-Cloud überwacht und repariert werden.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 11/20/2020
ms.openlocfilehash: 7f367a6ef9b86f235a16a698735cfb58a11f6862
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033594"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Überwachen und Reparieren von privaten Azure VMware Solution-Clouds

In Azure VMware Solution werden die VMware ESXi-Server ständig in einer privaten Azure VMware Solution-Cloud überwacht. 

## <a name="what-azure-vmware-solution-monitors"></a>Was wird von Azure VMware Solution überwacht?

In Azure VMware Solution wird Folgendes auf Fehlerbedingungen auf dem Host überwacht:  

- Prozessorstatus 
- Speicherstatus 
- Verbindung und Betriebszustand 
- Zustand des Hardwarelüfters 
- Verlust der Netzwerkkonnektivität 
- Zustand der Hardware-Hauptplatine 
- Aufgetretene Fehler auf den Datenträgern eines vSAN-Hosts 
- Hardwarespannung 
- Hardware-Temperaturstatus 
- Hardware-Betriebszustand 
- Speicherstatus 
- Verbindungsfehler 

> [!NOTE]
> Administratoren von Azure VMware Solution-Mandanten dürfen die obigen definierten VMware vCenter-Alarme nicht bearbeiten oder löschen, da diese über die Azure VMware Solution-Steuerungsebene in vCenter verwaltet werden. Diese Alarme werden von der Azure VMware Solution-Überwachung verwendet, um den Fehlerbehebungsprozess für den Azure VMware Solution-Host auszulösen.

## <a name="azure-vmware-solution-host-remediation"></a>Fehlerbehebung für Azure VMware Solution-Host  

Wenn von Azure VMware Solution ein Leistungsabfall oder ein Fehler auf dem Azure VMware Solution-Knoten auf einem Mandanten der privaten Cloud erkannt wird, wird der Fehlerbehebungsprozess für den Host ausgelöst. Die Fehlerbehebung für den Host umfasst das Ersetzen des fehlerhaften Knotens durch einen neuen fehlerfreien Knoten.  

Der Fehlerbehebungsprozess für den Host beginnt, indem dem Cluster ein neuer fehlerfreier Knoten hinzugefügt wird. Anschließend wird der fehlerhafte Host nach Möglichkeit in den VMware vSphere-Wartungsmodus versetzt. VMware vMotion wird verwendet, um die virtuellen Computer vom fehlerhaften Host auf andere verfügbare Server im Cluster zu verschieben. Hierbei ist es unter Umständen möglich, für die Workloads eine Livemigration ohne Ausfallzeiten durchzuführen. In Szenarien, in denen der fehlerhafte Host nicht in den Wartungsmodus versetzt werden kann, wird er aus dem Cluster entfernt.

## <a name="next-steps"></a>Nächste Schritte

Im folgenden finden Sie einige Themen mit weiteren Informationen:

- [Azure VMware Solution-Upgrades für private Clouds](concepts-upgrades.md)
- [Lebenszyklusverwaltung von Azure VMware Solution-VMs](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Schützen von Azure VMware Solution-VMs mit Azure Security Center-Integration](azure-security-integration.md)
- [Sichern von Azure VMware Solution-VMs mit Azure Backup Server](backup-azure-vmware-solution-virtual-machines.md)
- [Ausführen der Notfallwiederherstellung virtueller Computer mithilfe von Azure VMware Solution](disaster-recovery-for-virtual-machines.md)
