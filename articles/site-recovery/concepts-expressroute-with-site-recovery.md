---
title: Informationen zur Verwendung von ExpressRoute mit Azure Site Recovery
description: Beschreibt, wie Sie Azure ExpressRoute mit dem Azure Site Recovery-Dienst für Notfallwiederherstellung und Migration verwenden.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 99fa8d4cf8f48d0fe72da36baef20c83add438c0
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330256"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute mit Azure Site Recovery

Mit Microsoft Azure ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud erweitern. Mit ExpressRoute können Sie Verbindungen zu Microsoft Cloud Services wie Microsoft Azure, Microsoft 365 und Dynamics 365 herstellen.

In diesem Artikel wird beschrieben, wie Sie Azure ExpressRoute mit Azure Site Recovery für die Notfallwiederherstellung und die Migration verwenden können.

## <a name="expressroute-circuits"></a>ExpressRoute-Verbindungen

Eine ExpressRoute-Verbindung stellt eine logische Verbindung zwischen der lokalen Infrastruktur und den Microsoft-Clouddiensten über einen Konnektivitätsanbieter dar. Sie können mehrere ExpressRoute-Verbindungen bestellen. Alle Verbindungen können sich in derselben Region oder in verschiedenen Regionen befinden und über verschiedene Konnektivitätsanbieter mit dem jeweiligen Standort verbunden sein. Weitere Informationen zu ExpressRoute-Verbindungen finden Sie [hier](../expressroute/expressroute-circuit-peerings.md).

Einer ExpressRoute-Verbindung sind mehrere Routingdomänen zugeordnet. Weitere Informationen zu und eine Gegenüberstellung von ExpressRoute-Routingdomänen finden Sie [hier](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Replikation zwischen einem lokalen Standort und Azure mit ExpressRoute

Azure Site Recovery ermöglicht die Notfallwiederherstellung und Migration zu Azure für lokale [Hyper-V-Computer](hyper-v-azure-architecture.md), [virtuelle VMware-Computer](vmware-azure-architecture.md) und [physische Server](physical-azure-architecture.md). In allen Szenarien der Migration von einem lokalen Standort zu Azure werden die Replikationsdaten an ein Azure Storage-Konto gesendet und darin gespeichert. Während der Replikation zahlen Sie keine Gebühren für die VM. Wenn Sie einen Failover zu Azure ausführen, erstellt Site Recovery automatisch Azure-IaaS-VMs.

Site Recovery repliziert Daten über einen öffentlichen Endpunkt in einem Azure Storage-Konto oder auf verwalteten Replikatdatenträgern in der Azure-Zielregion. Um ExpressRoute für den Site Recovery-Replikationsdatenverkehr zu verwenden, können Sie [Microsoft-Peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) oder ein vorhandenes [öffentliches Peering](../expressroute/about-public-peering.md) (veraltet für neue Erstellungen) verwenden. Microsoft-Peering ist die empfohlene Routingdomäne für die Replikation. Beachten Sie, dass die Replikation über privates Peering nur unterstützt wird, wenn [für den Tresor private Endpunkte aktiviert sind](hybrid-how-to-enable-replication-private-endpoints.md).

Stellen Sie sicher, dass auch die [Netzwerkanforderungen](vmware-azure-configuration-server-requirements.md#network-requirements) für den Konfigurationsserver erfüllt sind. Der Konfigurationsserver muss für die Orchestrierung der Site Recovery-Replikation eine Verbindung mit bestimmten URLs herstellen können. Für diese Verbindung kann ExpressRoute nicht verwendet werden. 

Wenn Sie den Proxy vor Ort verwenden und ExpressRoute für den Replikationsdatenverkehr verwenden möchten, müssen Sie die Proxyumgehungsliste auf dem Konfigurationsserver und den Prozessservern konfigurieren. Führen Sie dafür die folgenden Schritte aus:

- Laden Sie das Tool PsExec [hier](/sysinternals/downloads/psexec) herunter, um auf den Systembenutzerkontext zuzugreifen.
- Öffnen Sie Internet Explorer im Systembenutzercontext, indem Sie die folgende Befehlszeile ausführen: psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Hinzufügen von Proxyeinstellungen in Internet Explorer
- Fügen Sie in der Umgehungsliste die Azure-Speicher-URL *.blob.core.windows.net hinzu.

Dadurch wird sichergestellt, dass nur Replikationsdatenverkehr über ExpressRoute fließt, während die Kommunikation über Proxy laufen kann.

Nach dem Failover virtueller Computer oder Server zu einem virtuellen Azure-Netzwerk können Sie mit [privatem Peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) auf sie zugreifen. 

Das kombinierte Szenario wird in der folgenden Abbildung dargestellt: ![Migration von einem lokalen Standort zu Azure mit ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikation von Azure zu Azure mit ExpressRoute

Azure Site Recovery ermöglicht die Notfallwiederherstellung für [Azure-VMs](azure-to-azure-architecture.md). Je nachdem, ob Ihre virtuellen Azure-Computer [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) verwenden, werden Replikationsdaten an ein Azure Storage-Konto oder einen verwalteten Replikatdatenträger in der Azure-Zielregion gesendet. Obwohl die Replikationsendpunkte öffentlich sind, läuft der Replikationsdatenverkehr für die Replikation von Azure VM nicht über das Internet, unabhängig davon, in welcher Azure-Region sich das virtuelle Quellnetzwerk befindet. Sie können die Standardsystemroute von Azure für das Adresspräfix 0.0.0.0/0 mit einer [benutzerdefinierten Route](../virtual-network/virtual-networks-udr-overview.md#custom-routes) überschreiben und VM-Datenverkehr auf ein lokales virtuelles Netzwerkgerät umleiten, aber diese Konfiguration wird für die Site Recovery-Replikation nicht empfohlen. Wenn Sie benutzerdefinierte Routen verwenden, sollten Sie [einen VNET-Dienstendpunkt](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in Ihrem virtuellen Netzwerk für „Storage“ erstellen, damit der Replikationsdatenverkehr innerhalb der Azure-Begrenzung bleibt.

Für die Notfallwiederherstellung einer Azure-VM wird ExpressRoute standardmäßig nicht für die Replikation benötigt. Nach dem Failover der virtuellen Computer zur Azure-Zielregion können Sie darauf über [privates Peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) zugreifen. Beachten Sie, dass Datenübertragungspreise unabhängig vom Modus der Azure-regionsübergreifenden Datenreplikation gelten.

Wenn Sie für die Verbindung zwischen Ihrem lokalen Datencenter und den Azure-VMs in der Quellregion bereits ExpressRoute verwenden, können Sie die Wiederherstellung der ExpressRoute-Verbindungen in der Failover-Zielregion planen. Sie können für die Verbindung zur Zielregion über eine neue virtuelle Netzwerkverbindung die gleiche ExpressRoute-Verbindung verwenden oder eine separate ExpressRoute-Verbindung und eine Verbindung für die Notfallwiederherstellung nutzen. Die verschiedenen möglichen Szenarien werden [hier](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute) beschrieben.

Sie können virtuelle Azure-Computer zu einer beliebigen Azure-Region innerhalb des gleichen geografischen Clusters replizieren, wie [hier](../site-recovery/azure-to-azure-support-matrix.md#region-support) beschrieben. Wenn sich die ausgewählte Azure-Zielregion nicht innerhalb der gleichen geopolitischen Region wie die Quelle befindet, müssen Sie möglicherweise ExpressRoute Premium aktivieren. Weitere Informationen finden Sie unter [ExpressRoute-Standorte](../expressroute/expressroute-locations.md) und [ExpressRoute – Preise](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [ExpressRoute-Verbindungen](../expressroute/expressroute-circuit-peerings.md)
- Weitere Informationen zu [ExpressRoute-Routingdomänen](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- Weitere Informationen zu [ExpressRoute-Standorten](../expressroute/expressroute-locations.md)
- Weitere Informationen zur Notfallwiederherstellung von [virtuellen Azure-Computern mit ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).