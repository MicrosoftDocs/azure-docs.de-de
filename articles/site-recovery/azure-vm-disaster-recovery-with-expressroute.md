---
title: Integrieren von Azure ExpressRoute mit Notfallwiederherstellung für virtuelle Azure-Computer mithilfe des Azure Site Recovery-Diensts | Microsoft-Dokumentation
description: Beschreibt, wie Sie die Notfallwiederherstellung für Azure VMs mithilfe von Azure Site Recovery und Azure ExpressRoute einrichten.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 90388d570d027aea3c897f7306a1714fd7e847b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772381"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrieren von Azure ExpressRoute mit Notfallwiederherstellung für virtuelle Azure-Computer


In diesem Artikel wird beschrieben, wie Sie Azure ExpressRoute mit [Azure Site Recovery](site-recovery-overview.md) integrieren, wenn Sie die Notfallwiederherstellung für Azure-VMs in einer sekundären Azure-Region einrichten.

Site Recovery ermöglicht die Notfallwiederherstellung von Azure-VMs durch Replikation der Daten von Azure-VMs zu Azure.

- Wenn Azure-VMs [verwaltete Azure-Datenträger](../virtual-machines/windows/managed-disks-overview.md) verwenden, werden VM-Daten auf einen replizierten verwalteten Datenträger in der sekundären Region repliziert.
- Wenn Azure-VMs keine verwalteten Datenträger verwenden, werden VM-Daten in ein Azure-Speicherkonto repliziert.
- Replikationsendpunkte sind öffentlich, aber der Replikationsdatenverkehr für virtuelle Azure-Computer wird nicht über das Internet übertragen.

Mit ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, auf die Microsoft Azure Cloud ausdehnen. Wenn Sie ExpressRoute konfiguriert haben, integriert es sich wie folgt mit Site Recovery:

- **Während der Replikation zwischen Azure-Regionen:** Replikationsdatenverkehr für die Notfallwiederherstellung von virtuellen Azure-Computern erfolgt nur innerhalb von Azure. ExpressRoute ist für die Replikation nicht erforderlich bzw. wird dafür nicht verwendet. Wenn Sie jedoch von einem lokalen Standort aus eine Verbindung mit den Azure-VMs im primären Azure-Standort herstellen, gibt es eine Reihe von Problemen zu beachten, wenn Sie die Notfallwiederherstellung für Azure-VMs einrichten.
- **Failover zwischen Azure-Regionen:** Bei Ausfällen führen Sie ein Failover von virtuellen Azure-Computern aus der primären in die sekundäre Azure-Region durch. Nach einem Failover in eine sekundäre Region müssen Sie eine Reihe von Schritten unternehmen, um auf die Azure-VMs in der sekundären Region per ExpressRoute zuzugreifen.


## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie mit den folgenden Konzepten vertraut sind:

- ExpressRoute-[Verbindungen](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute-[Routingdomänen](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute-[Standorte](../expressroute/expressroute-locations.md).
- [Architektur der Replikation](azure-to-azure-architecture.md) von Azure-VMs
- Vorgehensweise zum [Einrichten der Replikation](azure-to-azure-tutorial-enable-replication.md) für Azure-VMs.
- Vorgehensweise für ein [Failover](azure-to-azure-tutorial-failover-failback.md) von Azure-VMs.


## <a name="general-recommendations"></a>Allgemeine Empfehlungen

Als bewährte Methode und um effiziente Ziele für die Wiederherstellungszeit (Recovery Time Objectives, RTO) für die Notfallwiederherstellung zu gewährleisten, empfehlen wir, dass Sie wie folgt vorgehen, wenn Sie Site Recovery für die Integration mit ExpressRoute einrichten:

- Bereitstellen von Netzwerkkomponenten vor dem Failover in eine sekundäre Region:
    - Wenn Sie die Replikation für Azure-VMs aktivieren, kann Site Recovery Netzwerkressourcen wie Netzwerke, Subnetze und Gateways automatisch in der Azure-Zielregion bereitstellen, basierend auf den Quellnetzwerkeinstellungen.
    - Site Recovery kann Netzwerkressourcen wie VNet-Gateways nicht automatisch einrichten.
    - Es wird empfohlen, dass Sie diese zusätzlichen Netzwerkressourcen vor dem Failover bereitstellen. Mit dieser Bereitstellung ist eine kurze Ausfallzeit verbunden, die sich auf die gesamte Wiederherstellungszeit auswirken kann, wenn sie bei der Planung der Bereitstellung nicht berücksichtigt wurde.
- Ausführen regelmäßiger Übungen zur Notfallwiederherstellung:
    - Bei dem Verfahren wird Ihre Replikationsstrategie ohne Datenverlust oder Ausfallzeiten überprüft. Dies hat keinen Einfluss auf Ihre Produktionsumgebung. Dies hilft, Konfigurationsprobleme in letzter Minute zu vermeiden, die sich negativ auf die RTO-Zielvorgabe auswirken können.
    - Wenn Sie ein Testfailover für die Übung ausführen, wird empfohlen, ein separates Azure-VM-Netzwerk zu verwenden, und nicht das Standardnetzwerk, das bei der Aktivierung der Replikation eingerichtet wurde.
- Verwenden Sie unterschiedliche IP-Adressbereiche, wenn Sie über eine einzelne ExpressRoute-Leitung verfügen.
    - Es wird empfohlen, dass Sie einen anderen IP-Adressraum für das virtuelle Zielnetzwerk verwenden. Hierdurch werden Probleme beim Herstellen von Verbindungen bei regionalen Ausfällen vermieden.
    - Wenn Sie keinen separaten Adressraum verwenden können, stellen Sie sicher, dass Sie das Testfailover der Notfallwiederherstellungsübung in ein gesondertes Testnetzwerk mit anderen IP-Adressen ausführen. Sie können nicht zwei VNets mit sich überschneidenden IP-Adressräumen mit derselben ExpressRoute-Leitung verbinden.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replizieren virtueller Azure-Computer bei Verwendung von ExpressRoute


Wenn Sie die Replikation für Azure-VMs in einem primären Standort einrichten möchten, und Sie von Ihrem lokalen Standort aus über ExpressRoute mit diesen virtuellen Computern eine Verbindung herstellen, müssen Sie Folgendes tun:

1. [Aktivieren der Replikation](azure-to-azure-tutorial-enable-replication.md) für jeden virtuellen Azure-Computer.
2. Optional das Netzwerk von Site Recovery einrichten lassen:
    - Wenn Sie die Replikation konfigurieren und aktivieren, richtet Site Recovery Netzwerke, Subnetze und Gateway-Subnetze in der Azure-Zielregion so ein, dass sie mit denen in der Quellregion übereinstimmen. Site Recovery trifft auch eine Zuordnung zwischen den virtuellen Quell- und Zielnetzwerken.
    - Wenn Sie nicht möchten, dass Site Recovery dies automatisch ausführt, erstellen Sie die Netzwerkressourcen am Ziel, bevor Sie die Replikation aktivieren.
3. Erstellen anderer Netzwerkelemente:
    - Site Recovery erstellt keine Routingtabellen, VNet-Gateways, VNet-Gatewayverbindungen, kein VNet-Peering oder andere Netzwerkressourcen und -verbindungen in der sekundären Region.
    - Sie müssen diese zusätzlichen Netzwerkelemente in der sekundären Region selbst erstellen, und zwar zu einem beliebigen Zeitpunkt vor der Ausführung eines Failovers aus der primären Region.
    - Sie können [Wiederherstellungspläne](site-recovery-create-recovery-plans.md) und Automatisierungsskripts verwenden, um diese Netzwerkressourcen einzurichten und zu verbinden.
1. Wenn Sie ein virtuelles Netzwerkgerät (NVA) bereitgestellt haben, um den Fluss des Netzwerkdatenverkehrs zu steuern, beachten Sie Folgendes:
    - Die Standardsystemroute von Azure für die Azure-VM-Replikation ist „0.0.0.0/0“.
    - Üblicherweise wird bei NVA-Bereitstellungen eine Standardroute (0.0.0.0/0) festgelegt, die die Übermittlung von ausgehendem Internetdatenverkehr durch die NVA erzwingt. Die Standardroute wird verwendet, wenn keine andere, spezifische Routingkonfiguration gefunden wird.
    - Ist dies der Fall, kann die NVA eventuell überlastet werden, wenn der gesamte Replikationsdatenverkehr durch die NVA läuft.
    - Dieselbe Einschränkung gilt bei der Verwendung von Standardrouten für das Routing des gesamten Azure VM-Datenverkehrs zu lokalen Bereitstellungen.
    - In diesem Szenario empfehlen wir, dass Sie [einen Netzwerk-Dienstendpunkt](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in Ihrem virtuellen Netzwerk für den „Microsoft.Storage“-Dienst erstellen, damit der Replikationsdatenverkehr innerhalb der Azure-Grenze bleibt.

## <a name="replication-example"></a>Replikationsbeispiel

Die Workloads herkömmlicher Enterprise-Bereitstellungen werden normalerweise auf mehrere Azure-vNets verteilt, mit einem zentralen Konnektivitätshub für externe Konnektivität mit dem Internet als auch mit lokalen Standorten. Normalerweise wird eine Hub-Spoke-Topologie zusammen mit ExpressRoute verwendet.

![Konnektivität zwischen lokalem Datencenter und Azure mit ExpressRoute vor einem Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Apps sind in der Azure-Region „Asien, Osten“ bereitgestellt.
- **Spoke-vNets**. Apps sind in zwei Spoke-vNets bereitgestellt:
    - **Quell-VNET 1:** 10.1.0.0/24.
    - **Quell-VNET 2:** 10.2.0.0/24.
    - Jedes virtuelle Spoke-Netzwerk ist mit **Hub-vNet** verbunden.
- **Hub-vNet**. Es gibt ein **Quell-Hub-VNET**: 10.10.10.0/24.
  - Dieses Hub-vNet fungiert als Gatekeeper.
  - Die gesamte Kommunikation über mehrere Subnetze hinweg erfolgt über diesen Hub.
    - **Hub-vNet-Subnetze**. Das Hub-vNet verfügt über zwei Subnetze:
    - **NVA-Subnetz:** 10.10.10.0/25. Dieses Subnetz enthält ein virtuelles Netzwerkgerät (NVA, 10.10.10.10).
    - **Gatewaysubnetz**: 10.10.10.128/25. Dieses Subnetz enthält ein ExpressRoute-Gateway mit einer ExpressRoute-Verbindung. Diese dient dem Routing an den lokalen Standort über eine private Peering-Routingdomäne.
- Das lokale Datencenter besitzt eine ExpressRoute-Leitungsverbindung über einen Partner-Edge in Hongkong.
- Das gesamte Routing wird mittels Azure-Routingtabellen (UDR) gesteuert.
- Der gesamte ausgehende Datenverkehr zwischen VNets oder zum lokalen Datencenter wird über die NVA weitergeleitet.

### <a name="hub-and-spoke-peering-settings"></a>Einstellungen für Hub-Spoke-Peering

#### <a name="spoke-to-hub"></a>Spoke zu Hub

**Richtung** | **Einstellung** | **State**
--- | --- | ---
Spoke zu Hub | Virtuelle Netzwerkadressen zulassen | Enabled
Spoke zu Hub | Weitergeleiteten Datenverkehr zulassen | Enabled
Spoke zu Hub | Gatewaytransit zulassen | Deaktiviert
Spoke zu Hub | „Gateways entfernen“ verwenden | Enabled

 ![Konfiguration des Spoke-zu-Hub-Peerings](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub zu Spoke

**Richtung** | **Einstellung** | **State**
--- | --- | ---
Hub zu Spoke | Virtuelle Netzwerkadressen zulassen | Enabled
Hub zu Spoke | Weitergeleiteten Datenverkehr zulassen | Enabled
Hub zu Spoke | Gatewaytransit zulassen | Enabled
Hub zu Spoke | „Gateways entfernen“ verwenden | Deaktiviert

 ![Konfiguration des Hub-zu-Spoke-Peerings](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Beispielschritte

In unserem Beispiel sollte Folgendes passieren, wenn die Replikation für Azure-VMs im Quellnetzwerk aktiviert wird:

1. Sie [aktivieren die Replikation](azure-to-azure-tutorial-enable-replication.md) für eine VM.
2. Site Recovery erstellt Replikat-vNets, Subnetze und -Gateway-Subnetze in der Zielregion.
3. Site Recovery erstellt Zuordnungen zwischen den Quellnetzwerken und den Replikat-Zielnetzwerken, die es erstellt.
4. Sie erstellen manuell Gateways des virtuellen Netzwerks, virtuelle Netzwerk-Gateway-Verbindungen, virtuelles Netzwerk-Peering bzw. jegliche anderen Netzwerkressourcen oder -verbindungen.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Failover von Azure-VMs bei Verwendung von ExpressRoute

Nach dem Failover von Azure-VMS in die Azure-Zielregion mittels Site Recovery können Sie auf diese zugreifen, indem Sie [privates Peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) von ExpressRoute verwenden.

- Sie müssen ExpressRoute mittels einer neuen Verbindung mit dem Ziel-vNet verbinden. Die vorhandene ExpressRoute-Verbindung wird nicht automatisch übertragen.
- Die Art, in der Sie Ihre ExpressRoute-Verbindung mit dem Ziel-vNet einrichten, hängt von Ihrer ExpressRoute-Topologie ab.


### <a name="access-with-two-circuits"></a>Zugriff mit zwei Leitungen

#### <a name="two-circuits-with-two-peering-locations"></a>Zwei Leitungen mit zwei Peeringstandorten

Diese Konfiguration hilft beim Schutz von ExpressRoute-Leitungen vor regionalen Notfällen. Wenn Ihr primärer Peeringstandort ausfällt, können die Verbindungen vom anderen Standort aus weiterhin betrieben werden.

- Die mit der Produktionsumgebung verbundene Leitung ist normalerweise die primäre. Die sekundäre Leitung besitzt normalerweise eine niedrigere Bandbreite, die im Notfall erhöht werden kann.
- Nach einem Failover können Sie Verbindungen von der sekundären ExpressRoute-Leitung mit dem Ziel-vNet herstellen. Alternativ können Sie für den Eintritt eines Notfalls Verbindungen eingerichtet und bereit halten, um die Gesamtwiederherstellungszeit zu verringern.
- Mit gleichzeitigen Verbindungen mit sowohl primären als auch Ziel-vNets stellen Sie sicher, dass Ihr lokales Routing nur die sekundäre Leitung und Verbindung nur nach einem Failover verwendet.
- Die Quell- und Ziel-vNets können nach einem Failover neue IP-Adressen erhalten oder dieselben behalten. In beiden Fällen können die sekundären Verbindungen vor dem Failover eingerichtet werden.


#### <a name="two-circuits-with-single-peering-location"></a>Zwei Leitungen mit einzelnem Peeringstandort

Diese Konfiguration hilft Ihnen beim Schutz vor Ausfällen der primären ExpressRoute-Leitung, aber nicht, wenn der einzelne ExpressRoute-Peeringstandort ausfällt, was beide Leitungen beeinträchtigt.

- Sie können gleichzeitige Verbindungen zwischen dem lokalen Datencenter und dem Quell-vNet über die primäre Leitung und mit dem Ziel-vNet über die sekundäre Leitung nutzen.
- Mit gleichzeitigen Verbindungen mit primären als auch Ziel-vNets stellen Sie sicher, dass Ihr lokales Routing nur die sekundäre Leitung und Verbindung nach einem Failover verwendet.
-   Wenn die beiden Leitungen am selben Peeringstandort erstellt wurden, lassen sie sich nicht mit demselben vNet verknüpfen.

### <a name="access-with-a-single-circuit"></a>Zugriff mit einer einzelnen Leitung

In dieser Konfiguration gibt es nur eine ExpressRoute-Leitung. Obwohl die Leitung eine redundante Verbindung besitzt, für den Fall, dass eine ausfällt, bietet eine einzelne Routingleitung keine Resilienz, wenn Ihre Peeringregion ausfällt. Beachten Sie Folgendes:

- Sie können virtuelle Azure-Computer in eine beliebige Azure-Region innerhalb [desselben geografischen Standorts](azure-to-azure-support-matrix.md#region-support) replizieren. Wenn sich die Azure-Zielregion nicht innerhalb desselben Standorts wie die Quelle befindet, müssen Sie ExpressRoute Premium aktivieren, wenn Sie eine einzelne ExpressRoute-Leitung verwenden. Erfahren Sie mehr über [ExpressRoute-Standorte](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) und [ExpressRoute – Preise](https://azure.microsoft.com/pricing/details/expressroute/).
- Sie können Quell- und Ziel-vNets nicht gleichzeitig mit der Leitung verbinden, wenn in der Zielregion derselbe IP-Adressraum verwendet wird. Szenario:    
    -  Trennen Sie die Verbindung mit der Quelle, und stellen Sie dann die Verbindung mit dem Ziel her. Diese Verbindungsänderung kann als Teil eines Site Recovery-Wiederherstellungsplans geskriptet werden. Beachten Sie Folgendes:
        - Wenn bei einem regionalen Ausfall kein Zugriff auf die primäre Region möglich ist, kann der Trennvorgang fehlschlagen. Dies könnte sich auf die Herstellung einer Verbindung mit der Zielregion auswirken.
        - Wenn Sie die Verbindung in die Zielregion erstellt haben, und die primäre Region wird später wiederhergestellt, kann es zu Paketverlusten kommen, da zwei gleichzeitige Verbindungen versuchen, auf den gleichen Adressraum zuzugreifen.
        - Um dies zu verhindern, beenden Sie die primäre Verbindung sofort.
        - Nach einem VM-Failback in die primäre Region kann die primäre Verbindung nach dem Trennen der sekundären Verbindung erneut hergestellt werden.
-   Wenn im Ziel-vNet ein anderer Adressraum verwendet wird, lassen sich von derselben ExpressRoute-Leitung gleichzeitig Verbindungen mit den Quell- und Ziel-vNets herstellen.


## <a name="failover-example"></a>Beispiel für ein Failover

In unserem Beispiel verwenden wir die folgende Topologie:

- Zwei verschiedene ExpressRoute-Leitungen an zwei verschiedenen ExpressRoute-Peeringstandorten.
- Die private IP-Adresse für die Azure-VMs wird nach einem Failover beibehalten.
- Die Zielregion für die Wiederherstellung ist Azure „Asien, Südosten“.
- Eine sekundäre ExpressRoute-Leitungsverbindung wird über einen Partner-Edge in Singapur hergestellt.

Informationen zu einer einfachen Topologie, die eine einzelne ExpressRoute-Leitung mit derselben IP-Adresse nach einem Failover verwendet, [finden Sie in diesem Artikel](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Beispielschritte
Um die Wiederherstellung in diesem Beispiel zu automatisieren, gehen Sie wie folgt vor:

1. Befolgen Sie die Schritte zum Einrichten der Replikation.
2. [Führen Sie ein Failover der Azure-VMs](azure-to-azure-tutorial-failover-failback.md) mit diesen zusätzlichen Schritten während oder nach dem Failover durch.

    a. Erstellen Sie das Azure ExpressRoute-Gateway im Hub-vNet der Zielregion. Dies ist erforderlich, um das Ziel-Hub-vNet mit der ExpressRoute-Leitung zu verbinden.

    b. Erstellen Sie die Verbindung von dem Ziel-Hub-vNet mit der ExpressRoute-Zielleitung.

    c. Richten Sie die VNet-Peerings zwischen den virtuellen Hubnetzwerken der Zielregion und den virtuellen Spoke-Netzwerken ein. Die Peering-Eigenschaften in der Zielregion sind identisch mit denen in der Quellregion.

    d. Richten Sie die UDRs im VNet-Hub und die beiden Spoke-VNets ein.

    - Die Eigenschaften der zielseitigen UDRs sind identisch mit denen auf der Quellseite, wenn die gleichen IP-Adressen verwendet werden.
    - Bei unterschiedlichen IP-Adressen sollten die UDRs entsprechend geändert werden.


Erstellen Sie ein Skript für diese Verbindungsänderung als Teil eines [Wiederherstellungsplans](site-recovery-create-recovery-plans.md). Je nach Anwendungskonnektivität und Zeitaufwand für die Wiederherstellung können die oben genannten Schritte auch vor dem Start des Failovers abgeschlossen werden.

#### <a name="after-recovery"></a>Nach der Wiederherstellung

Nachdem Sie die VMs wiederhergestellt und die Konnektivität wieder eingerichtet haben, sieht die Wiederherstellungsumgebung wie folgt aus.

![Konnektivität zwischen lokalem Datencenter und Azure mit ExpressRoute nach einem Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Wiederherstellungspläne](site-recovery-create-recovery-plans.md) zum Automatisieren des App-Failovers verwenden.
