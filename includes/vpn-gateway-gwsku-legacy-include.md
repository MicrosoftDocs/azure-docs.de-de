---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177979"
---
Die alten VPN-Gateway-SKUs lauten wie folgt:

* Basic
* Standard
* HighPerformance

VPN Gateway verwendet nicht die UltraPerformance-Gateway-SKU. Informationen zur UltraPerformance-SKU finden Sie in der Dokumentation zu [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Beachten Sie bei Verwendung der alten SKUs Folgendes:

* Wenn Sie einen richtlinienbasierten VPN-Typ verwenden möchten, müssen Sie die Basic-SKU verwenden. Richtlinienbasierte VPNs (früher als statisches Routing bezeichnet) werden in anderen SKUs nicht unterstützt.
* BGP wird in der Basic-SKU nicht unterstützt.
* Die gemeinsame Verwendung von VPN Gateway und ExpressRoute wird in der Basic-SKU nicht unterstützt.
* Aktiv-Aktiv-S2S-VPN-Gatewayverbindungen können nur in der HighPerformance-SKU konfiguriert werden.