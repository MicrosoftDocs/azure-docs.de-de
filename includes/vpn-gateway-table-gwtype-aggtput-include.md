---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fb9e9ea0e126509697b4874bf1e5e0b6a380e7f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66425755"
---
|**SKU**   | **S2S/VNet-zu-VNet-<br>Tunnels** | **P2S<br> SSTP-Verbindungen** | **P2S<br>-IKEv2/OpenVPN-Verbindungen** | **Benchmark für<br>aggregierten Durchsatz** | **BGP** | **Zonenredundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | Maximal 10    | Maximal 128  | Nicht unterstützt  | 100 MBit/s  | Nicht unterstützt| Nein |
|**VpnGw1**| Maximal 30*   | Maximal 128  | Maximal 250       | 650 MBit/s  | Unterstützt | Nein |
|**VpnGw2**| Maximal 30*   | Maximal 128  | Maximal 500       | 1 GBit/s    | Unterstützt | Nein |
|**VpnGw3**| Maximal 30*   | Maximal 128  | Maximal 1000      | 1,25 GBit/s | Unterstützt | Nein |
|**VpnGw1AZ**| Maximal 30*   | Maximal 128  | Maximal 250       | 650 MBit/s  | Unterstützt | Ja |
|**VpnGw2AZ**| Maximal 30*   | Maximal 128  | Maximal 500       | 1 GBit/s    | Unterstützt | Ja |
|**VpnGw3AZ**| Maximal 30*   | Maximal 128  | Maximal 1000      | 1,25 GBit/s | Unterstützt | Ja |


(\*) Verwenden Sie [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md), wenn Sie mehr als 30 S2S-VPN-Tunnel benötigen.

* Der Benchmark für den aggregierten Durchsatz basiert auf Messungen von mehreren, durch ein Gateway aggregierten Tunneln. Der Benchmark für den aggregierten Durchsatz für ein VPN-Gateway besteht aus einer Kombination aus S2S und P2S. **Bei zahlreichen P2S-Verbindungen kann eine S2S-Verbindung aufgrund von Durchsatzeinschränkungen beeinträchtigt sein.** Aufgrund der Bedingungen für den Internetdatenverkehr und dem Verhalten Ihrer Anwendung kann der Benchmark für den aggregierten Durchsatz nicht garantiert werden.

* Diese Verbindungsgrenzwerte sind voneinander getrennt. Sie können beispielsweise 128 SSTP-Verbindungen und 250 IKEv2-Verbindungen in der SKU „VpnGw1“ nutzen.

* Informationen zu den Preisen finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Informationen zum SLA (Vereinbarung zum Servicelevel) finden Sie auf der [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/)-Seite.

* VpnGw1, VpnGw2 und VpnGw3 werden nur für VPN-Gateways unterstützt, die das Resource Manager-Bereitstellungsmodell verwenden.

* Die Basic-SKU wird als Legacy-SKU betrachtet. Für die Basic-SKU gelten bestimmte Featurebeschränkungen. Die Größe eines Gateways mit einer Basic-SKU kann nicht auf eine der neuen Gateway-SKUs festgelegt werden. Stattdessen müssen Sie zu einer neuen SKU wechseln und dazu Ihr VPN-Gateway löschen und neu erstellen. Vergewissern Sie sich vor der Verwendung der Basic-SKU, dass das von Ihnen benötigte Feature unterstützt wird.
