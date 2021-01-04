---
title: Beibehalten von IP-Adressen nach einem Azure-VM-Failover mit Azure Site Recovery
description: Dieser Artikel beschreibt, wie Sie IP-Adressen beibehalten, wenn bei der Notfallwiederherstellung mit Azure Site Recovery ein Failover für Azure-VMs auf eine sekundäre Region ausgeführt wird.
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023549"
---
# <a name="retain-ip-addresses-during-failover"></a>Beibehalten von IP-Adressen während eines Failovers

[Azure Site Recovery](site-recovery-overview.md) ermöglicht die Notfallwiederherstellung für virtuelle Azure-Computer durch Replizieren von virtuellen Computern in einer anderen Azure-Region, durch Ausführen eines Failovers bei einem Ausfall und durch Ausführen eines Failbacks in der primären Region, wenn der Normalzustand wiederhergestellt ist.

Während des Failovers soll die IP-Adressierung in der Zielregion möglicherweise identisch mit der in der Quellregion sein:

- Wenn Sie die Notfallwiederherstellung für virtuelle Azure-Computer aktivieren, werden die Zielressourcen in Site Recovery basierend auf den Einstellungen für die Quellressourcen erstellt. Für virtuelle Azure-Computer, die mit statischen IP-Adressen konfiguriert sind, wird in Site Recovery nach Möglichkeit die gleiche IP-Adresse für den virtuellen Zielcomputer bereitgestellt, sofern sie nicht bereits verwendet wird. Eine vollständige Erläuterung der Verarbeitung der Adressierung in Site Recovery finden Sie in [diesem Artikel](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Für einfache Anwendungen genügt die Standardkonfiguration. Für komplexere Anwendungen müssen Sie möglicherweise weitere Ressourcen bereitstellen, um sicherzustellen, dass die Konnektivität nach einem Failover funktioniert.


Dieser Artikel enthält einige Beispiele für die Beibehaltung von IP-Adressen in komplexeren Beispielszenarien. Die Beispiele umfassen Folgendes:

- Failover für ein Unternehmen, das alle Ressourcen in Azure ausführt
- Failover für ein Unternehmen mit einer Hybridbereitstellung und Ressourcen, die lokal sowie in Azure ausgeführt werden

## <a name="resources-in-azure-full-failover"></a>Ressourcen in Azure: Vollständiges Failover

Unternehmen A führt alle seine Anwendungen in Azure aus.

### <a name="before-failover"></a>Vor dem Failover

Die Architektur vor dem Failover sieht wie folgt aus.

- Unternehmen A verfügt über identische Netzwerke und Subnetze in Azure-Quellregionen und Azure-Zielregionen.
- Zur Reduzierung des RTO-Werts (Recovery Time Objective) verwendet das Unternehmen Replikatknoten für SQL Server Always On, Domänencontroller usw. Diese Replikatknoten befinden sich in einem anderen VNET in der Zielregion, sodass eine Site-to-Site-VPN-Konnektivität zwischen den Quell- und Zielregionen hergestellt werden kann. Dies ist nicht möglich, wenn in der Quelle und im Ziel der gleiche IP-Adressraum verwendet wird.  
- Vor dem Failover sieht die Netzwerkarchitektur folgendermaßen aus:
    - Die primäre Azure-Region ist „Asien, Osten“.
        - „Asien, Osten“ verfügt über ein VNET (**Quell-VNET**) mit dem Adressraum 10.1.0.0/16.
        - „Asien, Osten“ weist Workloads auf, die auf drei Subnetze im VNET aufgeteilt sind:
            - **Subnetz 1**: 10.1.1.0/24
            - **Subnetz 2**: 10.1.2.0/24
            - **Subnetz 3**: 10.1.3.0/24
    - Die sekundäre Azure-Region (Zielregion) ist „Asien, Südosten“.
        - „Asien, Südosten“ verfügt über ein Wiederherstellungs-VNET (**Wiederherstellungs-VNET**), das mit **Quell-VNET** identisch ist.
        - „Asien, Südosten“ verfügt über ein weiteres VNET (**Azure VNET**) mit dem Adressraum 10.2.0.0/16.
        - **Azure VNET** enthält ein Subnetz (**Subnetz 4**) mit dem Adressraum 10.2.4.0/24.
        - Die Replikatknoten für SQL Server Always On, Domänencontroller usw. befinden sich in **Subnetz 4**.
    - **Quell-VNET** und **Azure VNET** sind per Site-to-Site-VPN-Konnektivität verbunden.
    - Das **Wiederherstellungs-VNet** ist nicht mit einem anderen virtuellen Netzwerk verbunden.
    - **Unternehmen A** weist die IP-Zieladressen für replizierte Elemente zu und überprüft sie. Die Ziel-IP für jeden virtuellen Computer entspricht jeweils der Quell-IP.

![Ressourcen in Azure vor dem vollständigen Failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Nach dem Failover

Bei einem Ausfall in der Quellregion kann Unternehmen A ein Failover für alle Ressourcen in der Zielregion ausführen.

- Da die IP-Zieladressen bereits vor dem Failover eingerichtet wurden, kann Unternehmen A das Failover orchestrieren und nach dem Failover automatisch Verbindungen zwischen **Wiederherstellungs-VNET** und **Azure VNET** herstellen. Dies ist in der nachfolgenden Abbildung dargestellt.
- Je nach den Anwendungsanforderungen können Verbindungen zwischen den beiden VNETs (**Wiederherstellungs-VNET** und **Azure VNET**) in der Zielregion vor, während (als Zwischenschritt) oder nach dem Failover hergestellt werden.
  - Das Unternehmen kann [Wiederherstellungspläne](site-recovery-create-recovery-plans.md) verwenden, um anzugeben, wann die Verbindungen hergestellt werden.
  - Die Verbindungen zwischen den VNETs können per VNET-Peering oder per Site-to-Site-VPN hergestellt werden.
      - Beim VNET-Peering wird kein VPN-Gateway verwendet, und es gelten andere Einschränkungen.
      - Die [Preise](https://azure.microsoft.com/pricing/details/virtual-network) für VNET-Peering werden anders berechnet als die [Preise](https://azure.microsoft.com/pricing/details/vpn-gateway) für VPN Gateway (VNET-zu-VNET). Für Failover empfiehlt sich in der Regel die Verwendung der gleichen Verbindungsmethode wie in den Quellnetzwerken, einschließlich des Netzwerktyps, um unvorhersehbare Netzwerkvorfälle zu minimieren.

    ![Ressourcen in Azure: Vollständiges Failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Ressourcen in Azure: Failover für isolierte Apps

Möglicherweise müssen Sie ein Failover auf App-Ebene ausführen, beispielsweise ein Failover für eine bestimmte Anwendung oder Anwendungsebene, die sich in einem dedizierten Subnetz befinden.

- Obwohl Sie in diesem Szenario die IP-Adressierung beibehalten können, empfiehlt sich dies in der Regel nicht, da sich dadurch die Wahrscheinlichkeit von Konnektivitätsinkonsistenzen erhöht. Sie verlieren zusätzlich die Subnetzkonnektivität mit anderen Subnetzen in demselben Azure VNET.
- Eine bessere Möglichkeit zum Ausführen eines App-Failovers auf Subnetzebene besteht darin, unterschiedliche IP-Zieladressen für das Failover zu verwenden (wenn Konnektivität mit anderen Subnetzen im Quell-VNET erforderlich ist) oder jede App in einem eigenen dedizierten VNET in der Quellregion zu isolieren. Mit letzterem Ansatz können Sie die Konnektivität zwischen Netzwerken in der Quellregion einrichten und das gleiche Verhalten emulieren, wenn ein Failover in der Zielregion ausgeführt wird.  

In diesem Beispiel platziert Unternehmen A Apps in der Quellregion in dedizierten VNETs und stellt die Konnektivität zwischen diesen VNETs her. Bei diesem Entwurf kann ein Failover für isolierte Apps ausgeführt werden, und die privaten IP-Quelladressen können im Zielnetzwerk beibehalten werden.

### <a name="before-failover"></a>Vor dem Failover

Vor dem Failover sieht die Architektur folgendermaßen aus:

- Anwendungs-VMs werden in der primären Azure-Region „Asien, Osten“ gehostet:
    - **App1**-VMs befinden sich im VNET **Quell-VNET 1**: 10.1.0.0/16.
    - **App2**-VMs befinden sich im VNET **Quell-VNET 2**: 10.2.0.0/16.
    - **Quell-VNET 1** umfasst zwei Subnetze.
    - **Quell-VNET 2** umfasst zwei Subnetze.
- Die sekundäre Azure-Region (Zielregion) ist „Asien, Südosten“. - „Asien, Südosten“ verfügt über zwei Wiederherstellungs-VNETs (**Wiederherstellungs-VNET 1** und **Wiederherstellungs-VNET 2**), die mit **Quell-VNET 1** und **Quell-VNET 2** identisch sind.
        - **Wiederherstellungs-VNET 1** und **Wiederherstellungs-VNET 2** umfassen jeweils zwei Subnetze, die den Subnetzen in **Quell-VNET 1** und **Quell-VNET 2** entsprechen. - „Asien, Südosten“ verfügt über ein weiteres VNET (**Azure VNET**) mit dem Adressraum 10.3.0.0/16.
        - **Azure VNET** enthält ein Subnetz (**Subnetz 4**) mit dem Adressraum 10.3.4.0/24.
        Die Replikatknoten für SQL Server Always On, Domänencontroller usw. befinden sich in **Subnetz 4**.
- Es gibt mehrere Site-to-Site-VPN-Verbindungen: 
    - **Quell-VNET 1** und **Azure VNET**
    - **Quell-VNET 2** und **Azure VNET**
    - **Quell-VNET 1** und **Quell-VNET 2** sind per Site-to-Site-VPN verbunden.
- **Wiederherstellungs-VNET 1** und **Wiederherstellungs-VNET 2** sind mit keinen anderen VNETs verbunden.
- **Unternehmen A** konfiguriert VPN-Gateways für **Wiederherstellungs-VNET 1** und  **2**, um den RTO-Wert zu reduzieren.  
- **Wiederherstellungs-VNet1** und **Wiederherstellungs-VNet2** sind nicht mit einem anderen virtuellen Netzwerk verbunden.
- Zur Reduzierung des RTO-Werts (Recovery Time Objective) werden vor dem Failover VPN-Gateways für **Wiederherstellungs-VNet1** und **Wiederherstellungs-VNet2** konfiguriert.

    ![Ressourcen in Azure vor dem App-Failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Nach dem Failover

Bei einem Ausfall oder Problem mit Auswirkungen auf eine einzelne App (im Beispiel in **Quell-VNET 2) kann Unternehmen A die betroffene App wie folgt wiederherstellen:


- Trennen der VPN-Verbindungen zwischen **Quell-VNET 1** und **Quell-VNET 2** und zwischen **Quell-VNET 2** und **Azure VNET**
- Herstellen von VPN-Verbindungen zwischen **Quell-VNET 1** und **Wiederherstellungs-VNET 2** und zwischen **Wiederherstellungs-VNET 2** und **Azure VNET**
- Ausführen eines Failovers für die VMs in **Quell-VNET 2** zu **Wiederherstellungs-VNET 2**

![Ressourcen in Azure: App-Failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Dieses Beispiel kann auf weitere Anwendungen und Netzwerkverbindungen erweitert werden. Die Empfehlung besteht darin, bei Failovern von der Quelle zum Ziel, falls möglich, ein „Like-Like“-Verbindungsmodell zu verwenden.
- Für VPN-Gateways werden öffentliche IP-Adressen und Gatewayhops verwendet, um Verbindungen herzustellen. Wenn Sie keine öffentlichen IP-Adressen verwenden oder zusätzliche Hops vermeiden möchten, können Sie das [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md) in Azure verwenden, um virtuelle Netzwerke mittels Peering über [unterstützte Azure-Regionen](../virtual-network/virtual-network-manage-peering.md#cross-region) hinweg zu verknüpfen.

## <a name="hybrid-resources-full-failover"></a>Hybridressourcen: Vollständiges Failover

In diesem Szenario führt **Unternehmen B** eine Hybridbereitstellung aus, bei der ein Teil der Anwendungsinfrastruktur in Azure und die restliche Infrastruktur lokal ausgeführt wird. 

### <a name="before-failover"></a>Vor dem Failover

Vor dem Failover sieht die Netzwerkarchitektur wie folgt aus.

- Anwendungs-VMs werden in der Azure-Region „Asien, Osten“ gehostet.
- „Asien, Osten“ verfügt über ein VNET (**Quell-VNET**) mit dem Adressraum 10.1.0.0/16.
  - „Asien, Osten“ weist Workloads auf, die auf drei Subnetze in **Quell-VNET** aufgeteilt sind:
    - **Subnetz 1**: 10.1.1.0/24
    - **Subnetz 2**: 10.1.2.0/24
    - **Subnetz 3**: 10.1.3.0/24, in dem ein virtuelles Azure-Netzwerk mit dem Adressraum 10.1.0.0/16 verwendet wird. Dieses virtuelle Netzwerk hat den Namen **Quell-VNET**.
      - Die sekundäre Azure-Region (Zielregion) ist „Asien, Südosten“:
  - „Asien, Südosten“ verfügt über ein Wiederherstellungs-VNET (**Wiederherstellungs-VNET**), das mit **Quell-VNET** identisch ist.
- Für VMs in „Asien, Osten“ wird über Azure ExpressRoute oder Site-to-Site-VPN eine Verbindung mit einem lokalen Rechenzentrum hergestellt.
- Zur Reduzierung des RTO-Werts (Recovery Time Objective) stellt Unternehmen B vor dem Failover Gateways im Wiederherstellungs-VNET in der Azure-Region „Asien, Südosten“ bereit.
- Unternehmen B weist die IP-Zieladressen für replizierte virtuelle Computer zu und überprüft sie. Die IP-Zieladresse für jeden virtuellen Computer entspricht jeweils der IP-Quelladresse.


![Konnektivität vom lokalen Standort zu Azure vor dem Failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Nach dem Failover


Bei einem Ausfall in der Quellregion kann Unternehmen B ein Failover für alle Ressourcen in der Zielregion ausführen.

- Da die IP-Zieladressen bereits vor dem Failover eingerichtet wurden, kann Unternehmen B das Failover orchestrieren und nach dem Failover automatisch Verbindungen zwischen **Wiederherstellungs-VNET** und **Azure VNET** herstellen.
- Je nach den Anwendungsanforderungen können Verbindungen zwischen den beiden VNETs (**Wiederherstellungs-VNET** und **Azure VNET**) in der Zielregion vor, während (als Zwischenschritt) oder nach dem Failover hergestellt werden. Das Unternehmen kann [Wiederherstellungspläne](site-recovery-create-recovery-plans.md) verwenden, um anzugeben, wann die Verbindungen hergestellt werden.
- Die ursprüngliche Verbindung zwischen der Azure-Region „Asien, Osten“ und dem lokalen Rechenzentrum sollte getrennt werden, bevor die Verbindung zwischen der Azure-Region „Asien, Südosten“ und dem lokalen Rechenzentrum hergestellt wird.
- Das lokale Routing wird neu konfiguriert, um nach dem Failover auf die Zielregion und die Gateways zu verweisen.

![Konnektivität vom lokalen Standort zu Azure nach dem Failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybridressourcen: Failover für isolierte Apps

Unternehmen B kann kein Failover für isolierte Apps auf Subnetzebene ausführen. Der Grund dafür ist, dass der Adressraum im Quell-VNET und im Wiederherstellungs-VNET identisch ist und die Verbindung von der ursprünglichen Quelle zum lokalen Standort aktiv ist.

 - Für die Anwendungsresilienz muss Unternehmen B jede App in einem eigenen dedizierten Azure VNET anordnen.
 - Wenn sich jede App in einem separaten VNET befindet, kann Unternehmen B ein Failover für isolierte Apps ausführen und Quellverbindungen an die Zielregion leiten.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Wiederherstellungspläne](site-recovery-create-recovery-plans.md).
