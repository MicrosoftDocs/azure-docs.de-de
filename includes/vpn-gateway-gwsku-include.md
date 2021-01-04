---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010808"
---
Beim Erstellen eines Gateways des virtuellen Netzwerks müssen Sie die gewünschte Gateway-SKU angeben. Wählen Sie die SKU aus, die Ihre Anforderungen im Bezug auf Workloadtypen, Durchsätze, Funktionen und SLAs erfüllt. Informationen zu virtuellen Netzwerkgateway-SKUs in Azure-Verfügbarkeitszonen finden Sie unter [Gateway SKUs für Azure-Verfügbarkeitszonen](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKUs nach Tunnel, Verbindung und Durchsatz

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw-SKUs (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 und VpnGw5AZ) werden nur für das Resource Manager-Bereitstellungsmodell unterstützt. Für klassische virtuelle Netzwerke sollten weiterhin die alten SKUs (Legacy-SKUs) verwendet werden.
>  * Informationen zur Verwendung der Legacy-Gateway-SKUs (Basic, Standard und HighPerformance) finden Sie unter [Arbeiten mit SKUs für virtuelle Netzwerkgateways (Legacy-SKUs)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * Informationen zu ExpressRoute-Gateway-SKUs finden Sie unter [Informationen zu Gateways für virtuelle Netzwerke für ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Gateway-SKUs nach Featuregruppe

Die neuen VPN-Gateway-SKUs optimieren die für die Gateways bereitgestellten Features:

| **SKU**| **Funktionen**|
| ---    | ---         |
|**Basic** (\*\*)   | **Routenbasiertes VPN**: Zehn Tunnel für S2S/Verbindungen; keine RADIUS-Authentifizierung für P2S; kein IKEv2 für P2S<br>**Richtlinienbasiertes VPN**: (IKEv1): 1 S2S/Verbindungstunnel; kein P2S|
| **Alle Generation1- und Generation2-SKUs mit Ausnahme von Basic** | **Routenbasiertes VPN**: bis zu 30 Tunnel (\*), P2S, BGP, Aktiv/Aktiv, benutzerdefinierte IPsec-/IKE-Richtlinie, Koexistenz von ExpressRoute/VPN |
|        |             |

(*) Sie können „PolicyBasedTrafficSelectors“ konfigurieren, um eine Verbindung zwischen einem routenbasierten VPN-Gateway und mehreren lokalen richtlinienbasierten Firewallgeräten herzustellen. Ausführliche Informationen finden Sie unter [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Herstellen einer Verbindung zwischen VPN-Gateways und mehreren lokalen richtlinienbasierten VPN-Geräten mit PowerShell).

(\*\*) Die Basic-SKU wird als Legacy-SKU betrachtet. Für die Basic-SKU gelten bestimmte Featurebeschränkungen. Die Größe eines Gateways mit einer Basic-SKU kann nicht auf eine der neuen Gateway-SKUs festgelegt werden. Stattdessen müssen Sie zu einer neuen SKU wechseln und dazu Ihr VPN-Gateway löschen und neu erstellen.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Gateway-SKUs: Gegenüberstellung von Produktions- und Dev-Test-Workloads

Aufgrund der Unterschiede bei SLAs und Features werden die folgenden SKUs für die Produktion bzw. für Dev-Test-Workloads empfohlen:

| **Workload**                       | **SKUs**               |
| ---                                | ---                    |
| **Produktion, kritische Workloads** | Alle Generation1- und Generation2-SKUs mit Ausnahme von Basic |
| **Dev-Test oder Proof of Concept**   | Basic (\*\*)                 |
|                                    |                        |

(\*\*) Die Basic-SKU wird als Legacy-SKU betrachtet und bietet eingeschränkte Features. Vergewissern Sie sich vor der Verwendung der Basic-SKU, dass das von Ihnen benötigte Feature unterstützt wird.

Wenn Sie die alten SKUs (Legacy-SKUs) verwenden, werden für die Produktion die SKUs „Standard“ und „HighPerformance“ empfohlen. Informationen und Anweisungen für alte SKUs finden Sie unter [Arbeiten mit SKUs für virtuelle Netzwerkgateways (Legacy-SKUs)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
