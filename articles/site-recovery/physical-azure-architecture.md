---
title: Architektur für die Notfallwiederherstellung physischer Server in Azure mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel bietet einen Überblick über die Komponenten und die Architektur, die bei der Notfallwiederherstellung von lokalen physischen Servern in Azure mit dem Azure Site Recovery-Dienst verwendet werden.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 354a68d7d4d07657baa7044566dde8b7ed77ca63
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400067"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architektur der Notfallwiederherstellung physischer Server in Azure

Dieser Artikel beschreibt die Architektur und Prozesse, die mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) bei der Replikation, bei der Ausführung eines Failovers und beim Wiederherstellen von physischen Windows- und Linux-Servern zwischen einem lokalen Standort und Azure verwendet werden.


## <a name="architectural-components"></a>Komponenten der Architektur

Die folgende Tabelle und Grafik bietet eine Übersicht der Komponenten, die für die Replikation des physischen Servers in Azure verwendet werden.  

**Komponente** | **Anforderung** | **Details**
--- | --- | ---
**Azure** | Ein Azure-Abonnement und ein Azure-Netzwerk. | Replizierte Daten von lokalen physischen Computern werden auf verwalteten Azure-Datenträgern gespeichert. Azure-VMs werden mit den replizierten Daten erstellt, wenn Sie ein Failover von lokalen VMs in Azure ausführen. Für die Azure-VMs wird eine Verbindung mit dem virtuellen Azure-Netzwerk hergestellt, wenn diese erstellt werden.
**Konfigurationsserver** | Ein einzelner lokaler physischer Computer oder eine einzelne lokale VMware-VM wird bereitgestellt, um alle lokalen Komponenten von Site Recovery auszuführen. Die VM führt den Konfigurationsserver, Prozessserver und Masterzielserver aus. | Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation.
 **Prozessserver**  | Wird standardmäßig zusammen mit dem Konfigurationsserver installiert. | Fungiert als Replikationsgateway. Empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage.<br/><br/> Der Prozessserver installiert auch den Mobility Service auf Servern, die Sie replizieren möchten.<br/><br/> Bei zunehmender Größe der Bereitstellung können Sie zusätzlich separate Prozessserver hinzufügen, um größere Mengen von Replikationsdatenverkehr zu bewältigen.
 **Masterzielserver** | Wird standardmäßig zusammen mit dem Konfigurationsserver installiert. | Verarbeitet die Replikationsdaten während des Failbacks von Azure.<br/><br/> Bei größeren Bereitstellungen können Sie einen zusätzlichen, separaten Masterzielserver für das Failback hinzufügen.
**Replizierte Server** | Der Mobility Service wird auf jedem Server installiert, den Sie replizieren. | Es wird empfohlen, dass Sie die automatische Installation vom Prozessserver zulassen. Alternativ können Sie den Dienst manuell installieren oder eine automatisierte Bereitstellungsmethode wie System Center Configuration Manager verwenden.

**Physisch-zu-Azure-Architektur**

![Komponenten](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replikationsprozess

1. Sie richten die Bereitstellung ein, einschließlich lokaler Komponenten und Azure-Komponenten. Im Recovery Services-Tresor geben Sie die Quelle und das Ziel der Replikation an, richten den Konfigurationsserver ein, erstellen eine Replikationsrichtlinie und aktivieren die Replikation.
2. Computer replizieren gemäß der Replikationsrichtlinie, und eine erste Kopie der Serverdaten wird im Azure-Speicher repliziert.
3. Die Replikation von Deltaänderungen in Azure beginnt, nachdem die erste Replikation abgeschlossen wurde. Nachverfolgte Änderungen für einen Computer werden in einer HRL-Datei gespeichert.
    - Computer kommunizieren mit dem Konfigurationsserver über den Port HTTPS 443 für eingehenden Datenverkehr, um die Replikationsverwaltung durchzuführen.
    - Computer senden Replikationsdaten an den Prozessserver über den Port HTTPS 9443 für eingehenden Datenverkehr (Konfiguration möglich).
    - Der Konfigurationsserver orchestriert die Replikationsverwaltung mit Azure über Port HTTPS 443 für ausgehenden Datenverkehr.
    - Der Prozessserver empfängt Daten von Quellcomputern, optimiert und verschlüsselt sie und sendet sie über Port 443 für ausgehenden Datenverkehr an den Azure-Speicher.
    - Wenn Sie die Multi-VM-Konsistenz aktivieren, kommunizieren Computer in der Replikationsgruppe über den Port 20004 miteinander. Multi-VM wird verwendet, wenn Sie mehrere Computer in Replikationsgruppen zusammenfassen, für die bei einem Failover gemeinsame ausfallsichere und anwendungskonsistente Wiederherstellungspunkte verwendet werden. Dies ist hilfreich, wenn auf Computern dieselbe Workload ausgeführt wird und die Computer einheitlich sein müssen.
4. Der Datenverkehr wird auf öffentlichen Endpunkten des Azure-Speichers über das Internet repliziert. Alternativ hierzu können Sie das [öffentliche Peering](../expressroute/expressroute-circuit-peerings.md#publicpeering) von Azure ExpressRoute verwenden. Das Replizieren von Datenverkehr über ein Site-to-Site-VPN von einem lokalen Standort nach Azure wird nicht unterstützt.


**Physisch-zu-Azure-Replikationsprozess**

![Replikationsprozess](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Failover- und Failbackprozesse

Nachdem die Replikation eingerichtet ist und Sie einen Notfallwiederherstellungsdrill (Testfailover) ausgeführt haben, um zu prüfen, ob alles wie erwartet arbeitet, können Sie bei Bedarf ein Failover und Failback ausführen. Beachten Sie Folgendes:

- Geplante Failover werden nicht unterstützt.
- Sie müssen ein Failback auf einer lokalen virtuellen VMware-VM durchführen. Daher benötigen Sie eine lokale VMware-Infrastruktur, auch wenn Sie lokale physische Server nach Azure replizieren.
- Sie führen ein Failover für einen einzelnen Computer aus oder erstellen Wiederherstellungspläne, um ein Failover zusammen für mehrere Computer auszuführen.
- Wenn Sie ein Failover ausführen, werden Azure-VMs aus replizierten Daten im Azure-Speicher erstellt.
- Nachdem das erste Failover ausgelöst wird, committen Sie es, um von der Azure-VM auf die Workload zuzugreifen.
- Wenn Ihr primärer lokaler Standort wieder verfügbar ist, können Sie das Failback durchführen.
- Sie müssen eine Failbackinfrastruktur festlegen, die Folgendes enthält:
    - **Temporärer Prozessserver in Azure:** Sie legen für ein Failback von Azure eine Azure-VM fest, die als Prozessserver fungiert, um die Replikation von Azure zu verarbeiten. Nach Beendigung des Failbacks können Sie diese VM löschen.
    - **VPN-Verbindung:** Für das Failback benötigen Sie eine VPN-Verbindung (oder Azure ExpressRoute) zwischen dem Azure-Netzwerk und dem lokalen Standort.
    - **Separater Masterzielserver:** Der lokale Masterzielserver, der mit dem Konfigurationsserver in der lokalen VMware-VM installiert wurde, führt standardmäßig das Failback aus. Wenn Sie jedoch ein Failback für große Mengen an Datenverkehr ausführen müssen, sollten Sie einen separaten lokalen Masterzielserver einrichten.
    - **Failbackrichtlinie:** Für die Replikation zurück an den lokalen Standort benötigen Sie eine Failbackrichtlinie. Sie wurde automatisch erstellt, als Sie die Replikationsrichtlinie aus lokalen VMs in Azure erstellt haben.
    - **VMware-Infrastruktur:** Für das Failback benötigen Sie eine VMware-Infrastruktur. Ein Failback zu einem physischen Server ist nicht möglich.
- Nachdem die Komponenten vorhanden sind, tritt das Failback in drei Phasen auf:
    - Phase 1: Schützen Sie die Azure-VMs erneut, sodass sie die Replikation von Azure zurück zu den lokalen VMware-VMs durchführen.
    - Phase 2: Führen Sie ein Failover zum lokalen Standort aus.
    - Phase 3: Nachdem für Workloads das Failback ausgeführt wurde, aktivieren Sie die Replikation erneut.

**VMware-Failback von Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nächste Schritte

Absolvieren Sie [dieses Tutorial](physical-azure-disaster-recovery.md), um die Replikation von physischen Servern nach Azure zu aktivieren.
