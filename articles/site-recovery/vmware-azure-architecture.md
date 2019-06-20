---
title: Architektur der VMware-zu-Azure-Notfallwiederherstellung in Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Komponenten und Architektur, die beim Einrichten der Notfallwiederherstellung von lokalen virtuellen VMware-Computern in Azure mit Azure Site Recovery verwendet werden.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: f1fdbd143093beb9736e86b24b76843ad82b89f2
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418376"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architektur der Notfallwiederherstellung von VMware zu Azure

Dieser Artikel beschreibt die Architektur und Prozesse, die mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) beim Bereitstellen der Replikation für die Notfallwiederherstellung, bei der Ausführung eines Failovers und beim Wiederherstellen von virtuellen VMware-Computern (VMs) zwischen einem lokalen VMware-Standort und Azure verwendet werden.


## <a name="architectural-components"></a>Komponenten der Architektur

Die folgende Tabelle und Grafik enthält eine Übersicht über die Komponenten, die für die VMware-Notfallwiederherstellung in Azure verwendet werden.

**Komponente** | **Anforderung** | **Details**
--- | --- | ---
**Azure** | Ein Azure-Abonnement, ein Azure Storage-Konto für die Zwischenspeicherung, einen verwalteten Datenträger und ein Azure-Netzwerk. | Replizierte Daten von lokalen VMs werden in Azure Storage gespeichert. Azure-VMs werden mit den replizierten Daten erstellt, wenn Sie ein Failover von lokalen VMs in Azure ausführen. Für die Azure-VMs wird eine Verbindung mit dem virtuellen Azure-Netzwerk hergestellt, wenn diese erstellt werden.
**Konfigurationsservercomputer** | Ein einzelner lokaler Computer. Es wird empfohlen, diesen als eine VMware-VM auszuführen, die aus einer heruntergeladenen OVF-Vorlage bereitgestellt werden kann.<br/><br/> Auf dem Computer werden alle lokalen Site Recovery-Komponenten ausgeführt, einschließlich Konfigurationsserver, Prozessserver und Masterzielserver. | **Konfigurationsserver**: Koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation.<br/><br/> **Prozessserver** Wird standardmäßig auf dem Konfigurationsserver installiert. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage. Der Prozessserver installiert auch Azure Site Recovery Mobility Service auf VMs, die Sie replizieren möchten, und führt auf lokalen Computern eine automatische Ermittlung durch. Bei zunehmender Größe der Bereitstellung können Sie zusätzlich separate Prozessserver hinzufügen, um größere Mengen von Replikationsdatenverkehr zu bewältigen.<br/><br/> **Masterzielserver**: Wird standardmäßig auf dem Konfigurationsserver installiert. Er verarbeitet die Replikationsdaten während des Failbacks von Azure. Bei größeren Bereitstellungen können Sie einen zusätzlichen, separaten Masterzielserver für das Failback hinzufügen.
**VMware-Server** | VMware-Server werden auf lokalen vSphere ESXi-Servern gehostet. Zum Verwalten der Hosts wird ein vCenter-Server empfohlen. | Sie fügen während der Bereitstellung von Site Recovery VMware-Server in den Recovery Services-Tresor hinzu.
**Replizierte Computer** | Mobility Service wird auf jedem virtuellen VMware-Computer installiert, den Sie replizieren. | Es wird empfohlen, dass Sie die automatische Installation vom Prozessserver zulassen. Alternativ können Sie den Dienst manuell installieren oder eine automatisierte Bereitstellungsmethode wie System Center Configuration Manager verwenden.

**Vmware-zu-Azure-Architektur**

![Komponenten](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Replikationsprozess

1. Wenn Sie die Replikation für einen virtuellen Computer aktivieren, beginnt die erste Replikation in Azure Storage mithilfe der angegebenen Replikationsrichtlinie. Beachten Sie Folgendes:
    - Bei VMware-VMs erfolgt die Replikation auf Blockebene und nahezu kontinuierlich mithilfe des Mobility Service-Agents, der auf dem virtuellen Computer ausgeführt wird.
    - Es werden alle festgelegten Replikationsrichtlinieneinstellungen angewendet:
        - **RPO-Schwellenwert**: Diese Einstellung hat keine Auswirkungen auf die Replikation. Sie unterstützt bei der Überwachung. Ein Ereignis wird ausgelöst, und optional wird eine E-Mail gesendet, wenn das aktuelle RPO den von Ihnen angegebenen Schwellenwert überschreitet.
        - **Aufbewahrung des Wiederherstellungspunkts**. Diese Einstellung gibt an, wie weit Sie zurückgehen möchten, wenn es zu einer Unterbrechung kommt. Bei Premium-Speicher beträgt die maximale Aufbewahrungsdauer 24 Stunden. Bei Standardspeicher beträgt sie 72 Stunden. 
        - **App-konsistente Momentaufnahmen**: Abhängig von Ihren App-Anforderungen können alle 1 bis 12 Stunden App-konsistente Momentaufnahmen erstellt werden. Bei den Momentaufnahmen handelt es sich um Azure-Blob-Standardmomentaufnahmen. Der auf einem virtuellen Computer ausgeführte Mobility Service-Agent fordert eine VSS-Momentaufnahme in Übereinstimmung mit dieser Einstellung an und markiert diesen Zeitpunkt als anwendungskonsistenten Punkt im Replikationsstrom.

2. Der Datenverkehr wird über das Internet auf öffentliche Endpunkte von Azure Storage repliziert. Alternativ hierzu können Sie Azure ExpressRoute mit [Microsoft-Peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) verwenden. Das Replizieren von Datenverkehr über ein virtuelles privates Site-to-Site-Netzwerk (VPN) von einem lokalen Standort nach Azure wird nicht unterstützt.
3. Die Replikation von Deltaänderungen in Azure beginnt, nachdem die erste Replikation abgeschlossen wurde. Nachverfolgte Änderungen für einen Computer werden an den Prozessserver gesendet.
4. Die Kommunikation erfolgt folgendermaßen:

    - Virtuelle Computer kommunizieren mit dem lokalen Konfigurationsserver über den Port HTTPS 443 für eingehenden Datenverkehr, um die Replikationsverwaltung auszuführen.
    - Der Konfigurationsserver orchestriert die Replikation mit Azure über Port HTTPS 443 für ausgehenden Datenverkehr.
    - Virtuelle Computer senden Replikationsdaten an den Prozessserver (der auf dem Konfigurationsserver ausgeführt wird) über Port HTTPS 9443 für eingehenden Datenverkehr. Dieser Port kann geändert werden.
    - Der Prozessserver empfängt Replikationsdaten, optimiert und verschlüsselt sie und sendet sie über den ausgehenden Port 443 an den Azure-Speicher.
5. Die Replikationsdatenprotokolle werden zunächst in einem Cachespeicherkonto in Azure gespeichert. Diese Protokolle werden dann verarbeitet, und die Daten werden auf einem verwalteten Azure-Datenträger (als asr-Seed-Datenträger bezeichnet) gespeichert. Die Wiederherstellungspunkte werden auf diesem Datenträger erstellt.




**Replikationsprozess von VMware zu Azure**

![Replikationsprozess](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Failover- und Failbackprozesse

Nachdem die Replikation eingerichtet ist und Sie einen Notfallwiederherstellungsdrill (Testfailover) ausgeführt haben, um zu überprüfen, ob alles wie erwartet funktioniert, können Sie bei Bedarf ein Failover und ein Failback ausführen.

1. Sie können ein Failover für einen einzelnen Computer ausführen oder Wiederherstellungspläne erstellen, um ein Failover für mehrere virtuelle Computer gleichzeitig auszuführen. Ein Wiederherstellungsplan besitzt gegenüber dem Failover eines einzelnen Computers die folgenden Vorteile:
    - Sie können App-Abhängigkeiten modellieren, indem Sie alle virtuellen Computer in der App in einen einzelnen Wiederherstellungsplan einschließen.
    - Sie können Skripts und Azure-Runbooks hinzufügen und den Vorgang für manuelle Aktionen anhalten.
2. Nachdem das erste Failover ausgelöst wird, committen Sie es, um von der Azure-VM auf die Workload zuzugreifen.
3. Wenn Ihr primärer lokaler Standort wieder verfügbar ist, können Sie sich auf ein Failback vorbereiten. Damit das Failback ausgeführt werden kann, müssen Sie eine Failbackinfrastruktur festlegen, die Folgendes enthält:

    * **Temporärer Prozessserver in Azure**: Sie legen für ein Failback von Azure eine Azure-VM fest, die als Prozessserver fungiert, um die Replikation von Azure zu verarbeiten. Nach Beendigung des Failbacks können Sie diese VM löschen.
    * **VPN-Verbindung**: Für das Failback benötigen Sie eine VPN-Verbindung (oder ExpressRoute-Verbindung) zwischen dem Azure-Netzwerk und dem lokalen Standort.
    * **Separater Masterzielserver**: Der lokale Masterzielserver, der mit dem Konfigurationsserver in der lokalen VMware-VM installiert wurde, führt standardmäßig das Failback aus. Wenn Sie ein Failback für große Volumen von Datenverkehr ausführen müssen, richten Sie einen separaten lokalen Masterzielserver ein.
    * **Failbackrichtlinie**: Für die Replikation zurück an den lokalen Standort benötigen Sie eine Failbackrichtlinie. Diese Richtlinie wird automatisch erstellt, wenn Sie eine Replikationsrichtlinie aus lokalen VMs in Azure erstellen.
4. Nachdem die Komponenten vorhanden sind, erfolgt das Failback in drei Phasen:

    - Phase 1: Schützen Sie die Azure-VMs erneut, sodass sie die Replikation von Azure zurück zu den lokalen VMware-VMs durchführen.
    -  Phase 2: Führen Sie ein Failover zum lokalen Standort aus.
    - Phase 3: Nachdem für Workloads ein Failback ausgeführt wurde, aktivieren Sie erneut die Replikation für die lokalen VMs.
    
 
**VMware-Failback von Azure**

![Failback](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nächste Schritte

Absolvieren Sie [dieses Tutorial](vmware-azure-tutorial.md), um eine VMware-zu-Azure-Replikation zu ermöglichen.
