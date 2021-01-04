---
title: Informationen zu den Preisen von Virtual WAN
titleSuffix: Azure Virtual WAN
description: In diesem Artikel werden allgemeine Fragen zu den Preisen von Virtual WAN beschrieben.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 8ced9bb33be341d35904967092414676a6ffe3ac
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905535"
---
# <a name="about-virtual-wan-pricing"></a>Informationen zu den Preisen von Virtual WAN

Azure Virtual WAN vereint mehrere Netzwerk- und Sicherheitsdienste in einem vereinheitlichten Framework. Es basiert auf einer Hub-and-Spoke-Architektur, in der die Hubs von Microsoft verwaltet werden. Im Hub werden verschiedene Dienste bereitgestellt, z. B. VPN, ExpressRoute, Benutzer-VPN (Point-to-Site), Firewall, Routing usw.

Für jeden Dienst in Virtual WAN ist ein Preis festgelegt. Daher kann für Virtual WAN kein einzelner Preis angegeben werden. Der [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) stellt einen Mechanismus zum Ableiten der Kosten bereit, basierend auf den Diensten, die in einem virtuellen WAN bereitgestellt werden. In diesem Artikel werden häufig gestellte Fragen zu Preisen für Virtual WAN behandelt.

>[!NOTE]
>Aktuelle Preisinformationen finden Sie unter [Preise für Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

## <a name="common-pricing-questions"></a><a name="questions"></a>Häufige Fragen zu Preisen

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Was ist eine Skalierungseinheit?

Eine **Skalierungseinheit** stellt die Einheit für die aggregierte Kapazität von Site-to-Site (S2S), Point-to-Site (P2S) und ExpressRoute (ER) in einem virtuellen Hub dar. Beispiel:

* **1 S2S-VPN-Skalierungseinheit** impliziert ein VPN-Gateway mit einer Gesamtkapazität von 500 MBit/s (aus Gründen der Resilienz werden duale Instanzen bereitgestellt) in einem virtuellen Hub, das 0,361 USD/Stunde kostet.
* **1 ER-Skalierungseinheit** impliziert ein Gateway mit insgesamt 2 GBit/s in einem virtuellen Hub mit Kosten von 0,42 USD/Stunde.
* **5 ER-Skalierungseinheiten** implizieren ein Gateway mit insgesamt 10 GBit/s in einem VNET eines virtuellen Hubs mit dem Preis 0,42 × 5 USD/Stunde. Von der 6. bis zur 10. Skalierungseinheit wird der ER-Preis in Schritten von 0,25 USD/Stunde erhöht.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Was ist eine Verbindungseinheit?

Eine **Verbindungseinheit** gilt für beliebige lokale/Nicht-Microsoft-Endpunkte, die eine Verbindung mit Azure-Gateways herstellen. Bei einem Site-to-Site-VPN impliziert dieser Wert Verzweigungen. Bei einem Benutzer-VPN (Point-to-Site) impliziert dieser Wert Remotebenutzer. Bei ExpressRoute impliziert dieser Wert Verbindungen von ExpressRoute-Leitungen.<br>Beispiel:

* Eine Branchverbindung mit einem Azure-VPN in einem virtuellen Hub kostet 0,05 USD/Stunde. 100 Branchverbindungen mit einem virtuellen Azure-Hub kosten demnach 0,05 USD × 100/Stunde.

* Zwei ExpressRoute-Leitungsverbindungen mit einem virtuellen Hub kosten 0,05 USD × 2/Stunde.

* Drei Remotebenutzerverbindungen mit dem virtuellen Azure-Hub-P2S-Gateway kosten 0,03 USD × 3/Stunde.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Wie werden die Gebühren für die Datenübertragung berechnet?

* In Azure eingehender Datenverkehr wird nicht berechnet. Von Azure ausgehender Datenverkehr (über VPN-, ExpressRoute- oder Point-to-Site-Benutzer-VPN-Verbindungen) unterliegt den [Standardgebühren für Azure-Datenübertragungen](https://azure.microsoft.com/pricing/details/bandwidth/).

* Bei Datenübertragungen zwischen einem Virtual WAN-Hub und einem Virtual Remote-WAN-Remotehub oder -VNET in einer anderen Region als der Quellhub fallen Datenübertragungsgebühren für den Datenverkehr an, der vom Hub ausgeht. Beispiel: Ausgehender Datenverkehr von einem Hub in „USA, Osten“ an einen Hub in „USA, Westen“ wird mit 0,02 USD/GB in Rechnung gestellt. Es fallen keine Kosten für den eingehenden Datenverkehr am Hub in „USA, Westen“ an. Für den gesamten Datenverkehr zwischen den Hubs fallen Gebühren für die regionsübergreifende Datenübertragung (bzw. die Datenübertragung innerhalb eines Kontinents oder die interkontinentale Übertragung) an. Weitere Informationen erhalten Sie in der [Preisübersicht für Azure-Datenübertragungen](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Worin besteht der Unterschied zwischen einer Standard-Hubgebühr und einer Standard-Hubverarbeitungsgebühr?

Virtual WAN ist in zwei Varianten verfügbar:

* Ein **grundlegendes virtuelles WAN**, in dem Benutzer mehrere Hubs bereitstellen und VPN-Site-to-Site-Verbindungen nutzen können. Ein einfaches virtuelles WAN verfügt nicht über erweiterte Funktionen wie vollständig mit dem Internet verbundene Hubs, ExpressRoute-Konnektivität, Benutzer-VPN-/Point-to-Site-VPN-Konnektivität, transitive VNET-zu-VNET-Verbindungen, VPN- und ExpressRoute-Transitverbindungen, Azure-Firewall usw. Für Hubs in einem grundlegenden virtuellen WAN fällt keine Grundgebühr oder Datenverarbeitungsgebühr an.

* Ein **virtuelles Standard-WAN** bietet erweiterte Funktionen wie vollständig mit dem Internet verbundene Hubs, ExpressRoute-Konnektivität, Benutzer-VPN-/Point-to-Site-VPN-Konnektivität, transitive VNET-zu-VNET-Verbindungen, VPN- und ExpressRoute-Transitverbindungen, Azure-Firewall usw. Sämtliches Routing für virtuelle Hubs wird über Router bereitgestellt, die mehrere Dienste in einem virtuellen Hub unterstützen. Für den Hub besteht eine Grundgebühr von 0,25 USD/Stunde. Außerdem fallen für die Datenverarbeitung auf dem Router des virtuellen Hubs für VNET-zu-VNET-Transitverbindungen Gebühren an. Dies wird in der folgenden Abbildung veranschaulicht.

 In der nachstehenden **Beispielabbildung** sind zwei VNETs verfügbar: VNET 1 und VNET 2. Angenommen, von einem virtuellen Computer in VNET 1 werden Daten mit 1 GBit/s an einen anderen virtuellen Computer in VNET 2 gesendet. Die folgenden Gebühren fallen an:

* Grundgebühr für virtuelle Hubs von 0,25 USD/Stunde

* Datenverarbeitungsgebühr für virtuelle Hubs von 0,02 USD/GB für 1 GBit/s

**Beispiel**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Beispiel":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).

* Aktuelle Preise finden Sie unter [Preise für Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).