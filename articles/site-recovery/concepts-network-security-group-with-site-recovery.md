---
title: Netzwerksicherheitsgruppen mit Azure Site Recovery | Microsoft-Dokumentation
description: Beschreibt, wie Sie Netzwerksicherheitsgruppen mit Azure Site Recovery für Notfallwiederherstellung und Migration verwenden.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0c06283080a4ee51f863714e4c515672299b420d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773016"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Netzwerksicherheitsgruppen mit Azure Site Recovery

Netzwerksicherheitsgruppen werden verwendet, um den Netzwerkdatenverkehr auf Ressourcen in einem virtuellen Netzwerk zu beschränken. Eine [Netzwerksicherheitsgruppe (NSG)](../virtual-network/security-overview.md#network-security-groups) enthält eine Liste mit Sicherheitsregeln, die ein- oder ausgehenden Netzwerkdatenverkehr basierend auf IP-Adresse, Port und Protokoll (für die Quelle bzw. das Ziel) zulassen oder ablehnen.

Im Resource Manager-Bereitstellungsmodell können Netzwerksicherheitsgruppen Subnetzen oder einzelnen Netzwerkschnittstellen zugeordnet werden. Wenn eine NSG einem Subnetz zugeordnet ist, gelten die Regeln für alle Ressourcen, die mit dem Subnetz verbunden sind. Datenverkehr kann weiter eingeschränkt werden, indem eine NSG einzelnen Netzwerkschnittstellen in einem Subnetz zugeordnet wird, dem bereits eine NSG zugeordnet ist.

In diesem Artikel wird beschrieben, wie Sie Netzwerksicherheitsgruppen mit Azure Site Recovery verwenden können.

## <a name="using-network-security-groups"></a>Verwenden von Netzwerksicherheitsgruppen

Jedem einzelnen Subnetz kann null (0) oder eine NSG zugeordnet werden. Jeder einzelnen Netzwerkschnittstelle kann auch null (0) oder eine NSG zugeordnet werden. Daher sind effektiv zwei Datenverkehrseinschränkungen für eine VM möglich, indem zunächst eine NSG einem Subnetz und dann eine andere NSG der Netzwerkschnittstelle der VM zugeordnet wird. Die Anwendung der NSG-Regeln hängt in diesem Fall von der Richtung des Datenverkehrs und der Priorität der angewendeten Sicherheitsregeln ab.

Betrachten Sie ein einfaches Beispiel mit nur einer VM:
-   Die VM befindet sich innerhalb von **Contoso Subnet**.
-   **Contoso Subnet** ist **Subnet NSG** zugeordnet.
-   Der VM-Netzwerkschnittstelle ist außerdem die **VM-NSG** zugeordnet.

![NSG mit Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

In diesem Beispiel für eingehenden Datenverkehr wird die Subnetz-NSG zuerst ausgewertet. Sämtlicher über die Subnetz-NSG zugelassene Datenverkehr wird dann durch die VM-NSG ausgewertet. Das Gegenteil gilt für ausgehenden Datenverkehr, wo die VM-NSG zuerst ausgewertet wird. Sämtlicher über die VM-NSG zugelassene Datenverkehr wird dann durch die Subnetz-NSG ausgewertet.

Dies ermöglicht die Anwendung einer präzisen Sicherheitsregel. Angenommen, Sie möchten für einige Anwendungs-VMs (z.B. Front-End-VMs) eingehenden Internetzugriff unter einem Subnetz zulassen, aber eingehenden Internetzugriff für andere VMs (z.B. Datenbank- und andere Back-End-VMs) einschränken. In diesem Fall können Sie eine weniger strenge Regel auf die Subnetz-NSG anwenden, Internetdatenverkehr zulassen und durch das Verweigern des Zugriffs auf die VM-NSG den Zugriff auf bestimmte virtuelle Computer beschränken. Das gleiche kann für ausgehenden Datenverkehr angewendet werden.

Stellen Sie beim Einrichten der NSG-Konfigurationen sicher, dass die richtigen Prioritäten auf die [Sicherheitsregeln](../virtual-network/security-overview.md#security-rules) angewandt werden. Regeln werden in der Reihenfolge ihrer Priorität verarbeitet. Regeln mit niedrigeren Zahlen werden vor Regeln mit höheren Zahlen verarbeitet, weil die Priorität für niedrigere Zahlen höher ist. Nachdem sich für den Datenverkehr eine Übereinstimmung mit einer Regel ergibt, wird die Verarbeitung angehalten. Daher werden alle Regeln mit niedrigerer Priorität (höherer Zahl), die über die gleichen Attribute wie Regeln mit höheren Prioritäten verfügen, nicht verarbeitet.

Es ist für Sie unter Umständen nicht immer klar erkennbar, wenn Netzwerksicherheitsgruppen sowohl auf eine Netzwerkschnittstelle als auch auf ein Subnetz angewendet werden. Sie können die Aggregatregeln, die auf eine Netzwerkschnittstelle angewendet werden, überprüfen, indem Sie die [effektiven Sicherheitsregeln](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) für eine Netzwerkschnittstelle anzeigen. Sie können auch in [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) die Funktion [Überprüfen des IP-Flusses](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) verwenden, um zu ermitteln, ob die Kommunikation für eine Netzwerkschnittstelle in ein- oder ausgehender Richtung zulässig ist. Das Tool gibt an, ob die Kommunikation zugelassen ist und für welche Netzwerksicherheitsregel Datenverkehr zugelassen ist oder abgelehnt wird.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Probleme bei der Replikation zwischen einem lokalen Standort und Azure mit NSG

Azure Site Recovery ermöglicht die Notfallwiederherstellung und Migration zu Azure für lokale [Hyper-V-Computer](hyper-v-azure-architecture.md), [virtuelle VMware-Computer](vmware-azure-architecture.md) und [physische Server](physical-azure-architecture.md). In allen Szenarien der Migration von einem lokalen Standort zu Azure werden die Replikationsdaten an ein Azure Storage-Konto gesendet und darin gespeichert. Während der Replikation zahlen Sie keine Gebühren für die VM. Wenn Sie einen Failover zu Azure ausführen, erstellt Site Recovery automatisch Azure-IaaS-VMs.

Sobald nach einem Failover zu Azure VMs erstellt worden sind, kann mit NSGs der Netzwerkdatenverkehr zu dem virtuellen Netzwerk und den virtuellen Computern beschränkt werden. Site Recovery erstellt nicht NSGs als Teil des Failovervorgangs. Sie sollten die erforderlichen Azure-NSGs erstellen, bevor das Failover initiiert wird. Dann können Sie NSGs automatisch während des Failovers mithilfe von Automatisierungsskripts vom Failover betroffenen virtuellen Computern mit den leistungsstarken [Wiederherstellungsplänen](site-recovery-create-recovery-plans.md) von Site Recovery zuordnen.

Angenommen, die VM-Konfiguration ähnelt nach dem Failover dem oben beschriebenen [Beispielszenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups):
-   Sie können **Contoso VNet** und **Contoso Subnet** im Rahmen der DR-Planung in der Azure-Zielregion erstellen.
-   Sie können sowohl **Subnet NSG** als auch **VM NSG** als Teil der gesamten DR-Planung erstellen und konfigurieren.
-   **Subnet NSG** kann dann sofort **Contoso Subnet** zugeordnet werden, da sowohl NSG als auch Subnetz bereits verfügbar ist.
-   **VM NSG** kann während des Failovers mit Wiederherstellungsplänen virtuellen Computern zugeordnet werden.

Sobald die NSGs erstellt und konfiguriert sind, sollten Sie ein [Testfailover](site-recovery-test-failover-to-azure.md) ausführen, um die im Skript vorgenommenen NSG-Zuordnungen und die VM-Konnektivität nach dem Failover zu überprüfen.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikation von Azure zu Azure mit NSG

Azure Site Recovery ermöglicht die Notfallwiederherstellung für [Azure-VMs](azure-to-azure-architecture.md). Beim Aktivieren der Replikation für Azure-VMs kann Site Recovery das Replikat virtueller Netzwerke (einschließlich Subnetze und Gatewaysubnetze) in der Zielregion erstellen und die erforderlichen Mappings zwischen den virtuellen Quell- und Zielnetzwerken ausführen. Sie können die Zielnetzwerke und -subnetze jedoch auch im Voraus erstellen und sie beim Aktivieren der Replikation verwenden. Site Recovery erstellt vor dem [Failover](azure-to-azure-tutorial-failover-failback.md) keine virtuellen Computer in der Azure-Zielregion.

Stellen Sie für die Azure-VM-Replikation sicher, dass die NSG-Regeln in der Azure-Quellregion [ausgehende Konnektivität](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) für den Replikationsdatenverkehr zulassen. Sie können diese erforderlichen Regeln auch über diese [NSG-Beispielkonfiguration](azure-to-azure-about-networking.md#example-nsg-configuration) testen und überprüfen.

Site Recovery erstellt bzw. repliziert keine NSGs als Teil des Failovervorgangs. Sie sollten die erforderlichen NSGs in der Azure-Zielregion erstellen, bevor das Failover initiiert wird. Dann können Sie NSGs automatisch während des Failovers mithilfe von Automatisierungsskripts vom Failover betroffenen virtuellen Computern mit den leistungsstarken [Wiederherstellungsplänen](site-recovery-create-recovery-plans.md) von Site Recovery zuordnen.

Berücksichtigen Sie das weiter oben beschriebene [Beispielszenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups):
-   Site Recovery kann Replikate von **Contoso VNet** und **Contoso Subnet** in der Azure-Zielregion erstellen, wenn die Replikation für den virtuellen Computer aktiviert ist.
-   Sie können die gewünschten Replikate von **Subnet NSG** und **VM NSG** (z.B. mit dem Namen **Zielsubnetz-NSG** bzw. **Ziel-VM-NSG**) in der Azure-Zielregion erstellen, wobei zusätzliche in der Zielregion erforderliche Regeln zugelassen sind.
-   **Zielsubnetz-NSG** kann dann sofort dem Zielregionssubnetz zugeordnet werden, da sowohl NSG als auch Subnetz bereits verfügbar ist.
-   **Ziel-VM-NSG** kann während des Failovers mit Wiederherstellungsplänen virtuellen Computern zugeordnet werden.

Sobald die NSGs erstellt und konfiguriert sind, sollten Sie ein [Testfailover](azure-to-azure-tutorial-dr-drill.md) ausführen, um die im Skript vorgenommenen NSG-Zuordnungen und die VM-Konnektivität nach dem Failover zu überprüfen.

## <a name="next-steps"></a>Nächste Schritte
-   Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md#network-security-groups).
-   Erfahren Sie mehr über NSG-[Sicherheitsregeln](../virtual-network/security-overview.md#security-rules).
-   Erfahren Sie mehr über [effektive Sicherheitsregeln](../virtual-network/diagnose-network-traffic-filter-problem.md) für eine NSG.
-   Informieren Sie sich ausführlicher über [Wiederherstellungspläne](site-recovery-create-recovery-plans.md) zum Automatisieren des Anwendungsfailovers.
