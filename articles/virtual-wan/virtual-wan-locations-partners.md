---
title: Azure Virtual WAN – Partner und Standorte | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Liste der Standorte von Azure Virtual WAN-Partnern und -Hubs.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 188bc14de502640ae05f1d1eb55330d86cee50b4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621478"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Standorte von Virtual WAN-Partnern und -Hubs

Dieser Artikel enthält Informationen über die von Virtual WAN unterstützten Regionen und Partner für Konnektivität mit Virtual Hub.

Der Netzwerkdienst Azure Virtual WAN bietet optimierte und automatisierte Konnektivität zwischen Branches über Azure. Per Virtual WAN können Sie Branchgeräte für die Kommunikation mit Azure verbinden und konfigurieren. Dies ist entweder manuell oder durch die Nutzung von Geräten eines Anbieters über einen Virtual WAN-Partner möglich. Die Nutzung von Geräten eines Partners ermöglicht eine einfache Verwendung, die Vereinfachung der Verbindungsherstellung und die Konfigurationsverwaltung.

Konnektivität auf dem lokalen Gerät wird auf automatisierte Weise mit dem virtuellen Hub hergestellt. Ein virtueller Hub ist ein von Microsoft verwaltetes virtuelles Netzwerk. Der Hub enthält verschiedene Dienstendpunkte zum Aktivieren der Konnektivität über Ihr lokales Netzwerk (vpnsite). Pro Region ist nur ein Hub möglich.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Zweigstellen: Automatisierung der IPSec-Konnektivität durch Partner

Geräte, die sich mit Azure Virtual WAN verbinden, verfügen für die Verbindungsherstellung über eine integrierte Automatisierung. Diese wird typischerweise auf der Geräteverwaltungsoberfläche (o.ä.) eingerichtet. Dadurch wird die Verbindungs- und Konfigurationsverwaltung zwischen dem VPN-Zweigstellengerät und einem Azure Virtual Hub VPN-Endpunkt (VPN Gateway) eingerichtet.

Die folgende allgemeine Automatisierung wird in der Gerätekonsole bzw. im Verwaltungscenter eingerichtet:

* Entsprechende Berechtigungen für das Gerät, um auf die Azure Virtual WAN-Ressourcengruppe zugreifen zu können
* Hochladen des Zweigstellengeräts in Azure Virtual WAN
* Automatischer Download der Azure-Konnektivitätsinformationen
* Konfiguration des lokalen Zweigstellengeräts 

Einige Konnektivitätspartner können die Automatisierung erweitern, indem sie das Azure Virtual Hub-VNET und das VPN-Gateway erstellen. Mehr über Automatisierung erfahren Sie unter [Automatisierungsrichtlinien für Virtual WAN-Partner](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Zweigstellen: Partner für IPSec-Konnektivität

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Die folgenden Partner sind gemäß einer zwischen den Firmen unterzeichneten Vereinbarung über den Auftragsumfang zum Automatisieren der IPsec-Konnektivität zwischen dem Partnergerät und Azure Virtual WAN-VPN-Gateways vorgesehen: 128 Technologies, Arista, F5 Networks, Oracle SD-WAN (Talari) und SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partner mit integrierten Virtual Hub-Angeboten
Zusätzlich zur Verwendung von automatisierter IPSec-Konnektivität für Zweigstellen bieten einige Partner auch **virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs)** an, die direkt in den Azure Virtual WAN-Hub integriert werden können.  Kunden haben hierbei die Option, ihre Zweigstellenverbindungen auf einer kompatiblen Appliance eines Drittanbieters im Virtual WAN-Hub enden zu lassen.  

Partner, die NVAs im Virtual WAN-Hub anbieten, müssen folgende Voraussetzungen erfüllen:

* Implementierung einer Automatisierung der IPSec-Konnektivität über ihr Zweigstellengerät und Onboarding ihres NVA-Angebots für den Azure Virtual WAN-Hub.
* Vorhandenes Angebot für ein virtuelles Netzwerkgerät auf dem Azure Marketplace.

Wenn Sie Partner sind und Fragen zum Angebot von verwalteten NVAs im Virtual WAN-Hub haben, können Sie eine E-Mail an vwannvaonboarding@microsoft.com senden.

## <a name="integrated-virtual-hub-nva-partners"></a>Partner für integrierte Virtual WAN-Hub-NVA
Diese Partner verfügen über Angebote für **verwaltete Anwendungen**, die jetzt für die Bereitstellung im Virtual WAN-Hub verfügbar sind.

|Partner|Konfiguration/Anleitung/Bereitstellungsleitfaden|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Bereitstellungshandbuch für Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Cisco Cloud Service Router (CSR) vWAN](https://aka.ms/ciscoMarketPlaceOffer)| Während der Public Preview von Cisco Cloud Services (CSR) WAN in VWAN-Hub verlangt Cisco, dass sich der Endkunde als Cisco EFT-Kunde (Early Field Trial) registrieren muss, indem er eine E-Mail an vwan_public_preview@external.cisco.com sendet und das Bereitstellungshandbuch für vManage anfordert. |

Es ist vorgesehen, dass die folgenden Partner in naher Zukunft Angebote für NVAs im Virtual Hub bereitstellen: Aviatrix, Citrix, VeloCloud und Versa Networks.

## <a name="locations"></a><a name="locations"></a>Standorte

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Virtual WAN – Häufig gestellte Fragen](virtual-wan-faq.md).

* Weitere Informationen zur Automatisierung der Konnektivität mit Azure Virtual WAN finden Sie unter [Automatisierungsrichtlinien für Virtual WAN-Partner](virtual-wan-configure-automation-providers.md).
