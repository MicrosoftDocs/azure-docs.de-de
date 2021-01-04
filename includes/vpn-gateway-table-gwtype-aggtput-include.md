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
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010767"
---
|**VPN<br>Gateway-<br>Generation** |**SKU**   | **S2S/VNet-zu-VNet-<br>Tunnels** | **P2S<br> SSTP-Verbindungen** | **P2S<br>-IKEv2/OpenVPN-Verbindungen** | **Benchmark für<br>aggregierten Durchsatz** | **BGP** | **Zonenredundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation 1**|**Grundlegend**   | Maximal 10    | Maximal 128  | Nicht unterstützt  | 100 MBit/s  | Nicht unterstützt| Nein |
|**Generation 1**|**VpnGw1**  | Maximal 30*   | Maximal 128  | Maximal 250       | 650 MBit/s  | Unterstützt | Nein |
|**Generation 1**|**VpnGw2**  | Maximal 30*   | Maximal 128  | Maximal 500       | 1 GBit/s    | Unterstützt | Nein |
|**Generation 1**|**VpnGw3**  | Maximal 30*   | Maximal 128  | Maximal 1000      | 1,25 GBit/s | Unterstützt | Nein |
|**Generation 1**|**VpnGw1AZ**| Maximal 30*   | Maximal 128  | Maximal 250       | 650 MBit/s  | Unterstützt | Ja |
|**Generation 1**|**VpnGw2AZ**| Maximal 30*   | Maximal 128  | Maximal 500       | 1 GBit/s    | Unterstützt | Ja |
|**Generation 1**|**VpnGw3AZ**| Maximal 30*   | Maximal 128  | Maximal 1000      | 1,25 GBit/s | Unterstützt | Ja |
|        |            |            |           |                |           |           |     |
|**Generation 2**|**VpnGw2**  | Maximal 30*   | Maximal 128  | Maximal 500       | 1,25 GBit/s | Unterstützt | Nein |
|**Generation 2**|**VpnGw3**  | Maximal 30*   | Maximal 128  | Maximal 1000      | 2,5 GBit/s  | Unterstützt | Nein |
|**Generation 2**|**VpnGw4**  | Maximal 30*   | Maximal 128  | Maximal 5\.000      | 5 GBit/s    | Unterstützt | Nein |
|**Generation 2**|**VpnGw5**  | Maximal 30*   | Maximal 128  | Maximal 10000      | 10 GBit/s   | Unterstützt | Nein |
|**Generation 2**|**VpnGw2AZ**| Maximal 30*   | Maximal 128  | Maximal 500       | 1,25 GBit/s | Unterstützt | Ja |
|**Generation 2**|**VpnGw3AZ**| Maximal 30*   | Maximal 128  | Maximal 1000      | 2,5 GBit/s  | Unterstützt | Ja |
|**Generation 2**|**VpnGw4AZ**| Maximal 30*   | Maximal 128  | Maximal 5\.000      | 5 GBit/s    | Unterstützt | Ja |
|**Generation 2**|**VpnGw5AZ**| Maximal 30*   | Maximal 128  | Maximal 10000      | 10 GBit/s   | Unterstützt | Ja |

(\*) Verwenden Sie [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md), wenn Sie mehr als 30 S2S-VPN-Tunnel benötigen.

* Größenänderungen für VpnGw-SKUs sind innerhalb der gleichen Generation möglich. Dies gilt jedoch nicht für die Basic-SKU. Die Basic-SKU ist eine Legacy-SKU und unterliegt Featureeinschränkungen. Wenn Sie von der Basic-SKU zu einer anderen VpnGw-SKU wechseln möchten, müssen Sie das VPN-Gateway der Basic-SKU löschen und ein neues Gateway mit der gewünschten Generation und SKU-Größe erstellen.

* Diese Verbindungsgrenzwerte sind voneinander getrennt. Sie können beispielsweise 128 SSTP-Verbindungen und 250 IKEv2-Verbindungen in der SKU „VpnGw1“ nutzen.

* Informationen zu den Preisen finden Sie auf der Seite [Preise](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Informationen zum SLA (Vereinbarung zum Servicelevel) finden Sie auf der [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/)-Seite.

* Mit einem einzelnen Tunnel kann maximal ein Durchsatz von 1 GBit/s erzielt werden. Der Benchmark für den aggregierten Durchsatz in der obigen Tabelle basiert auf Messungen für mehrere, über ein einzelnes Gateway aggregierte Tunnel. Der Benchmark für den aggregierten Durchsatz für ein VPN-Gateway besteht aus einer Kombination aus S2S und P2S. **Bei zahlreichen P2S-Verbindungen kann eine S2S-Verbindung aufgrund von Durchsatzeinschränkungen beeinträchtigt sein.** Aufgrund der Bedingungen für den Internetdatenverkehr und dem Verhalten Ihrer Anwendung kann der Benchmark für den aggregierten Durchsatz nicht garantiert werden.

Damit unsere Kunden die relative Leistung von SKUs mit unterschiedlichen Algorithmen besser nachvollziehen können, haben wir für die Leistungsermittlung die öffentlich verfügbaren Tools iperf und CTSTraffic verwendet. Die folgende Tabelle enthält die Ergebnisse von Leistungstests für VpnGw-SKUs der ersten Generation. Wie Sie sehen, wird die beste Leistung erzielt, wenn sowohl für die IPSec-Verschlüsselung als auch für die Integrität der GCMAES256-Algorithmus verwendet wird. Bei Verwendung von AES256 für die IPSec-Verschlüsselung und SHA256 für die Integrität wurde eine durchschnittliche Leistung erzielt. Bei Verwendung von DES3 für die IPSec-Verschlüsselung und SHA256 für die Integrität wurde die geringste Leistung erzielt.

|**Generation**|**SKU**   | **Verwendete<br>Algorithmen** | **Ermittelter<br>Durchsatz** | **Ermittelte<br>Pakete pro Sekunde** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation 1**|**VpnGw1**| GCMAES256<br>AES256 und SHA256<br>DES3 und SHA256| 650 MBit/s<br>500 MBit/s<br>120 MBit/s   | 58.000<br>50.000<br>50.000|
|**Generation 1**|**VpnGw2**| GCMAES256<br>AES256 und SHA256<br>DES3 und SHA256| 1 GBit/s<br>500 MBit/s<br>120 MBit/s | 90.000<br>80.000<br>55.000|
|**Generation 1**|**VpnGw3**| GCMAES256<br>AES256 und SHA256<br>DES3 und SHA256| 1,25 GBit/s<br>550 MBit/s<br>120 MBit/s | 105.000<br>90.000<br>60.000|
|**Generation 1**|**VpnGw1AZ**| GCMAES256<br>AES256 und SHA256<br>DES3 und SHA256| 650 MBit/s<br>500 MBit/s<br>120 MBit/s   | 58.000<br>50.000<br>50.000|
|**Generation 1**|**VpnGw2AZ**| GCMAES256<br>AES256 und SHA256<br>DES3 und SHA256| 1 GBit/s<br>500 MBit/s<br>120 MBit/s | 90.000<br>80.000<br>55.000|
|**Generation 1**|**VpnGw3AZ**| GCMAES256<br>AES256 und SHA256<br>DES3 und SHA256| 1,25 GBit/s<br>550 MBit/s<br>120 MBit/s | 105.000<br>90.000<br>60.000|
