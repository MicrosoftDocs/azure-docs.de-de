---
title: Informationen zu Azure Site Recovery | Microsoft-Dokumentation
description: Hier erhalten Sie eine Übersicht über den Azure Site Recovery-Dienst und eine Zusammenfassung der Bereitstellungsszenarien für Notfallwiederherstellung und Migration.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 907d12464156f551930098f6bd6a6a24596307cb
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479925"
---
# <a name="about-site-recovery"></a>Informationen zu Azure Site Recovery

Willkommen beim Azure Site Recovery-Dienst! Dieser Artikel enthält eine kurze Dienstübersicht.

Organisationen benötigen eine BCDR-Strategie (Business Continuity and Disaster Recovery, Geschäftskontinuität und Notfallwiederherstellung), die den Schutz ihrer Daten sowie die Verfügbarkeit ihrer Apps und Workloads gewährleistet, wenn geplante oder ungeplante Ausfälle auftreten.

Azure Recovery Services unterstützen Sie bei Ihrer BCDR-Strategie:

- **Site Recovery-Dienst**: Site Recovery sorgt dafür, dass geschäftliche Apps und Workloads während Ausfällen weiter ausgeführt werden, und trägt so zur Aufrechterhaltung der Geschäftskontinuität bei. Site Recovery repliziert Workloads, die auf physischen und virtuellen Computern ausgeführt werden, von einem primären Standort an einen sekundären Standort. Im Falle eines Ausfalls am primären Standort können Sie ein Failover auf den sekundären Standort ausführen und von dort aus auf die Apps zugreifen. Wenn der primäre Standort dann wieder verfügbar ist, können Sie ein Failback ausführen.  
- **Backup-Dienst**: Der Dienst [Azure Backup](https://docs.microsoft.com/azure/backup/) sichert Ihre Daten in Azure und sorgt so dafür, dass Ihre Daten sicher und wiederherstellbar bleiben.

Site Recovery kann die Replikation für folgende Bereiche verwalten:

- Replikation von virtuellen Azure-Computern zwischen Azure-Regionen
- Lokale virtuelle Computer, virtuelle Azure Stack-Computer und physische Server


## <a name="what-does-site-recovery-provide"></a>Was bietet Site Recovery?


**Feature** | **Details**
--- | ---
**Einfache BCDR-Lösung** | Mit Site Recovery können Sie Replikation, Failover und Failback von einem zentralen Ort im Azure-Portal aus einrichten und verwalten.
**Replikation virtueller Azure-Computer** | Sie können die Notfallwiederherstellung virtueller Azure-Computer aus einer primären Region in einer sekundären Region einrichten.
**Replikation lokaler virtueller Computer** | Sie können lokale virtuelle Computer und physische Server in Azure oder in einem sekundären Datencenter replizieren. Mit der Replikation in Azure entfallen die Kosten und die Komplexität, die mit der Verwaltung eines sekundären Datencenters verbunden sind.
**Workloadreplikation** | Sie können beliebige Workloads replizieren, die auf unterstützten virtuellen Azure-Computern, auf lokalen virtuellen Hyper-V- und VMware-Computern und auf physischen Windows-/Linux-Servern ausgeführt werden.
**Datenresilienz** | Site Recovery orchestriert die Replikation, ohne dass dies Auswirkungen auf die Anwendungsdaten hat. Wenn Sie Daten in Azure replizieren, werden diese in Azure Storage gespeichert, und Sie profitieren von der entsprechenden Resilienz. Bei einem Failover werden virtuelle Azure-Computer auf der Grundlage der replizierten Daten erstellt.
**RTO- und RPO-Ziele** | Halten Sie die Grenzwerte Ihrer Organisation für Recovery Time Objectives (RTO) und Recovery Point Objectives (RPO) ein. Site Recovery ermöglicht eine fortlaufende Replikation für virtuelle Azure-Computer und virtuelle VMware-Computer sowie eine Replikationsfrequenz von nur 30 Sekunden für Hyper-V. Mittels Integration in [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) können Sie RTO weiter reduzieren.
**Beibehalten der Konsistenz für Apps bei einem Failover** | Bei der Replizierung können Sie Wiederherstellungspunkte mit anwendungskonsistenten Momentaufnahmen verwenden. Diese Momentaufnahmen umfassen Datenträgerdaten, sämtliche Daten im Arbeitsspeicher sowie alle aktiven Transaktionen.
**Testen ohne Unterbrechungen** | Sie können komfortabel Übungen zur Notfallwiederherstellung durchführen, ohne die laufende Replikation zu beeinflussen.
**Flexible Failover** | Sie können geplante Failover ohne Datenverlust für erwartete Ausfälle oder ungeplante Failover mit minimalem Datenverlust (je nach Replikationsfrequenz) für unerwartete Notfälle ausführen. Wenn der primäre Standort wieder verfügbar ist, kann problemlos ein Failback ausgeführt werden.
**Angepasste Wiederherstellungspläne** | Mit Wiederherstellungsplänen können Sie Failover und Wiederherstellungen von Anwendungen mit mehreren Ebenen anpassen und sequenzieren, die auf mehreren virtuellen Computern ausgeführt werden. In einem Wiederherstellungsplan können Sie Computer zusammenfassen und bei Bedarf Skripts und manuelle Aktionen hinzufügen. Wiederherstellungspläne können in Azure Automation-Runbooks integriert werden.
**BCDR-Integration** | Site Recovery ist in andere BCDR-Technologien integriert. Beispielsweise können Sie Site Recovery verwenden, um das SQL Server-Back-End von geschäftlichen Workloads zu schützen – mit nativer Unterstützung für SQL Server Always On zum Verwalten des Failovers von Verfügbarkeitsgruppen.
**Azure Automation-Integration** | Eine umfassende Azure Automation-Bibliothek bietet produktionsbereite, anwendungsspezifische Skripts, die heruntergeladen und in Site Recovery integriert werden können.
**Netzwerkintegration** | Site Recovery ist in Azure integriert, um eine einfache Anwendungsnetzwerkverwaltung zu ermöglichen, z.B. das Reservieren von IP-Adressen, Konfigurieren von Lastenausgleichsmodulen und Integrieren von Azure Traffic Manager zur Durchführung von effizienten Netzwerkwechseln.


## <a name="what-can-i-replicate"></a>Was kann ich replizieren?

**Unterstützt** | **Details**
--- | ---
**Replikationsszenarien** | Replizieren Sie virtuelle Azure-Computer aus einer Azure-Region in eine andere.<br/><br/>  Replizieren Sie lokale virtuelle VMware-Computer, virtuelle Hyper-V-Computer, physische Server (Windows und Linux) und virtuelle Azure Stack-Computer in Azure.<br/><br/> <br/><br/> Replizieren Sie AWS-Windows-Instanzen nach Azure.<br/><br/> Replizieren Sie lokale virtuelle VMware-Computer, von System Center VMM verwaltete virtuelle Hyper-V-Computer und physische Server an einem sekundären Standort.
**Regionen** | Informationen zu unterstützten Regionen für Site Recovery finden Sie [hier](https://azure.microsoft.com/regions/services/). |
**Replizierte Computer** | Überprüfen Sie die Replikationsanforderungen für die Replikation von [Azure-VMs](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [lokale VMware-VMs und physische Server](vmware-physical-azure-support-matrix.md#replicated-machines) sowie [lokale Hyper-V-VMs](hyper-v-azure-support-matrix.md#replicated-vms).
**Workloads** | Sie können jede Workload replizieren, die auf einem für die Replikation geeigneten Computer ausgeführt wird. Darüber hinaus hat das Site Recovery-Team App-spezifische Tests für eine [Reihe von Apps](site-recovery-workload.md#workload-summary) durchgeführt.



## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich ausführlicher über [Workloadunterstützung](site-recovery-workload.md).
* [Erste Schritte](azure-to-azure-quickstart.md) mit der Replikation virtueller Azure-Computer zwischen Regionen 
