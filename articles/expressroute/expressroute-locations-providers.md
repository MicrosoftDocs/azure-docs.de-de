---
title: 'Standorte und Konnektivitätsanbieter: Azure ExpressRoute | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine ausführliche Übersicht über die Standorte, an denen Dienste angeboten werden, sowie Informationen darüber, wie Sie eine Verbindung mit den Azure-Regionen herstellen können. Sortiert nach Standort.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: jaredr80
ms.openlocfilehash: c443d4705da83a43af10763f633f340b1eab69d3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478160"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-Partner und Peeringstandorte

> [!div class="op_single_selector"]
> * [Standorte nach Anbieter](expressroute-locations.md)
> * [Anbieter nach Standort](expressroute-locations-providers.md)


In den Tabellen in diesem Artikel finden Sie Informationen zu ExpressRoute-Konnektivitätsanbietern, zum geografischen Geltungsbereich von ExpressRoute, zu Microsoft-Clouddiensten, die über ExpressRoute unterstützt werden, und zu ExpressRoute-Systemintegratoren (SIs).

## <a name="partners"></a>ExpressRoute-Konnektivitätsanbieter
ExpressRoute wird in allen Azure-Regionen und an allen Standorten unterstützt. Die folgende Karte zeigt die Azure-Regionen und ExpressRoute-Standorte. ExpressRoute-Standorte beziehen sich auf jene Orte, an denen Microsoft eine Peeringkooperation mit mehreren Service Providern bietet.

![Standortkarte][0]

Wenn Sie mit mindestens einem ExpressRoute-Standort innerhalb der geopolitischen Region eine Verbindung hergestellt haben, haben Sie Zugriff auf die Azure-Dienste in allen Regionen dieser geopolitischen Region. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region
Die folgende Tabelle bietet eine Übersicht über die Azure-Regionen mit ExpressRoute-Standorten in einer geopolitischen Region.

| **Geopolitische Region** | **Zone** | **Azure-Regionen** | **ExpressRoute-Standorte** |
| --- | --- | --- | --- |
| **Australische Behörden** | 1 | Australien, Mitte; Australien, Mitte 2 |Canberra, Canberra2 |
| **Europa** | 1 |„Frankreich, Mitte“, „Frankreich, Süden“, „Europa, Norden“, „Europa, Westen“, „Vereinigtes Königreich, Westen“, „Vereinigtes Königreich, Süden“ |Amsterdam, Amsterdam2, Dublin, Frankfurt, London, London2, Marseille, Newport(Wales), Paris, Zürich |
| **Nordamerika** | 1 |USA, Osten; USA, Westen; USA, Osten 2; USA, Westen 2; USA, Mitte; USA, Süden-Mitte; USA, Norden-Mitte; USA, Westen-Mitte; Kanada, Mitte; Kanada, Osten |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asien** | 2 |Ostasien, Südostasien |Hongkong (SAR), Kuala Lumpur, Singapur, Singapur2, Taipeh |
| **Australien** | 2 |Südostaustralien, Ostaustralien |Melbourne, Perth, Sydney | 
| **Indien** | 2 |Indien, Westen, Indien, Mitte, Indien, Süden |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | 2 |Japan West, Japan Ost |Osaka, Tokio |
| **Südkorea** | 2 |Korea, Mitte, Korea, Süden |Busan, Seoul|
| **Vereinigte Arabische Emirate** | 3 | VAE, Mitte; VAE, Norden | Dubai, Dubai2 |
| **Südafrika** | 3 |„Südafrika, Westen“, „Südafrika, Norden“ |Kapstadt, Johannesburg |
| **Südamerika** | 3 |Brasilien Süd |Sao Paulo |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regionen und geopolitische Grenzen für nationale Clouds
In der folgenden Tabelle finden Sie Informationen zu Regionen und geopolitischen Grenzen für nationale Clouds.

| **Geopolitische Region** | **Azure-Regionen** | **ExpressRoute-Standorte** |
| --- | --- | --- |
| **US-Government Cloud** |„US Gov Arizona“, „US Gov Iowa“, „US Gov Texas“, „US Gov Virginia“, „US DoD, Mitte“, „US DoD, Osten“  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **China, Osten** |China, Osten; China, Osten2 |Shanghai, Shanghai2 |
| **China, Norden** |China, Norden; China, Norden2 |Peking, Peking2 |
| **Deutschland** |Deutschland, Mitte, Deutschland, Ost |Berlin, Frankfurt |

Konnektivität zwischen geopolitischen Regionen wird bei der ExpressRoute-Standard-SKU nicht unterstützt. Sie müssen das ExpressRoute Premium-Add-On aktivieren, um Unterstützung für globale Konnektivität zu erhalten. Verbindungen mit nationalen Cloudumgebungen werden nicht unterstützt. Wenden Sie sich an Ihren Konnektivitätsanbieter, wenn Sie derartige Verbindungen implementieren möchten.

## <a name="locations"></a>Standorte von Konnektivitätsanbietern

Die folgende Tabelle enthält die Konnektivitätsstandorte und die dazugehörigen Service Providers. Sie können auch eine Tabelle anzeigen ([Standorte nach Service Provider](expressroute-locations.md#locations)), die nach Service Providern mit den jeweiligen Standorten sortiert ist. 

**Lokale Azure-Regionen** sind diejenigen, auf die [ExpressRoute Local](expressroute-faqs.md) an jedem Peeringstandort zugreifen kann. **–** gibt an, dass ExpressRoute Local an diesem Peeringstandort nicht verfügbar ist.


### <a name="production-azure"></a>Azure-Produktionsumgebungen
| **Location** | **Besitzer des Peeringstandorts** | **Lokale Azure-Regionen** | **Service Providers** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Europa, Westen | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Europa, Westen | DE-CIX, Interxion |
| **Atlanta** | Equinix | – | Equinix, Megaport |
| **Busan** |LG CNS | Korea, Süden | LG CNS |
| **Canberra** | CDC | Australien, Mitte | CDC |
| **Canberra2** | CDC | Australien, Mitte 2| CDC |
| **Kapstadt** | Teraco | Südafrika, Westen | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Indien (Süden) | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Indien (Süden) | Airtel |
| **Chicago** | Equinix | USA Nord Mitte | Aryaka Networks, AT&T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Dallas** | Equinix | – | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | CoreSite | USA, Westen-Mitte | CoreSite, Megaport, Zayo |
| **Dubai** | Etisalat (VAE) | Vereinigte Arabische Emirate, Norden | Etisalat (VAE) |
| **Dubai2** | du datamena | Vereinigte Arabische Emirate, Norden | du datamena, Orixcom |
| **Dublin** | Equinix | Nordeuropa | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | – | DE-CIX, Interxion |
| **Hongkong SAR** | Equinix | Asien, Osten | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Johannesburg** | Teraco | Südafrika, Norden | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Kuala Lumpur** | TIME dotCom | – | TIME dotCom |
| **Las Vegas** | Switch | – | CenturyLink Cloud Connect, Megaport |
| **London** | Equinix | UK, Süden | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Telehouse | UK, Süden | IX Reach, Equinix |
| **Los Angeles** | CoreSite | – | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |Interxion | Frankreich, Süden | Interxion |
| **Melbourne** | NextDC | Australien, Südosten | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | – | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | – | Bell Canada, Cologix, Telus, Zayo |
| **Mumbai** | Tata Communications | Indien, Westen | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | Indien, Westen | Airtel, Sify, Vodafone Idea |
| **New York** | Equinix | – | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | Daten der nächsten Generation | UK, Westen | British Telecom, Colt, Level 3 Communications, Next Generation Data |
| **Osaka** | Equinix | Japan, Westen | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** | Interxion | Frankreich, Mitte | Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | – | Megaport, NextDC |
| **Quebec City** | 4Degrees | Kanada, Osten | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | USA Süd Mitte | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brasilien Süd | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | USA, Westen 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | KINX | Korea, Mitte | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | USA (Westen) | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, InterCloud, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Verizon, Zayo |
| **Silicon Valley2** | Coresite | USA (Westen) | Coresite | 
| **Singapur** | Equinix | Asien, Südosten | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | Global Switch | Asien, Südosten | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Sydney** | Equinix | Australien (Osten) | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, TPG Telecom, Verizon |
| **Taipeh** | Chief Telecom | – | Chief Telecom, FarEasTone |
| **Tokio** | Equinix | Japan, Osten | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. – IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Kanada, Mitte | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington DC** | Equinix | USA, Osten, USA, Osten 2 | Aryaka Networks, AT&T NetBond, British Telecom, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | Coresite | USA, Osten, USA, Osten 2 |Coresite | 
| **Zürich** | Interxion | – | Interxion |

 **+** steht für "In Kürze"

### <a name="national-cloud-environments"></a>Nationale Cloudumgebungen

### <a name="us-government-cloud"></a>US-Government Cloud
| **Location** | **Service Providers** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington DC** |AT&T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Location** | **Service Providers** |
| --- | --- |
| **Peking** |China Telecom |
| **Peking2** | China Telecom, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | China Telecom, GDS |

Weitere Informationen finden Sie unter [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Deutschland
| **Standort** | **Service Providers** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Konnektivität über Exchange-Anbieter
Wenn Ihr Konnektivitätsanbieter nicht in den vorherigen Abschnitten enthalten ist, können Sie dennoch eine Verbindung erstellen.

* Fragen Sie Ihren Konnektivitätsanbieter, ob eine Verbindung mit einem der Exchange-Standorte aus der vorherigen Tabelle bereitgestellt werden kann. Sie können auch die folgenden Links überprüfen, um weitere Informationen über die von den Exchange-Anbietern angebotenen Dienste zu erhalten. Viele Konnektivitätsanbieter sind bereits mit Ethernet-Exchanges verbunden.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Bitten Sie Ihren Konnektivitätsanbieter, Ihr Netzwerk auf den Peeringstandort Ihrer Wahl zu erweitern.
  * Stellen Sie sicher, dass Ihr Konnektivitätsanbieter Ihre Konnektivität mit hoher Verfügbarkeit erweitert, sodass es keine einzelnen Fehlerquellen mehr gibt.
* Bestellen Sie eine ExpressRoute-Verbindung mit der Exchange als Konnektivitätsanbieter für die Verbindung mit Microsoft.
  * Führen Sie die Schritte in [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, um die Verbindung einzurichten.

## <a name="c1partners"></a>Konnektivität über zusätzliche Dienstanbieter
| **Location** | **Exchange** | **Konnektivitätsanbieter** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telia |
| **Kapstadt** | Teraco | MTN |
| **Chicago** | Equinix | Lightower, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **Hamburg** | Equinix | Cinia |
| **Hongkong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Lightower, Spectrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **São Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Equinix | Cox Business, Spectrum Enterprise, Windstream |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies, Inc. Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Gtt Communications Inc, Epsilon Telecommunications Limited, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-Systemintegratoren
Je nach Ihrer Netzwerkgröße kann das Aktivieren privater Verbindungen für Ihre Anforderungen problematisch sein. Alle Systemintegratoren, die in der folgenden Tabelle aufgeführt sind, können Ihnen beim Integrieren von ExpressRoute helfen.

| **Kontinent** | **Systemintegratoren** |
| --- | --- |
| **Asien** |Avanade Inc., OneAs1a |
| **Australien** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Nordamerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Südamerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
* Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Standortkarte"
