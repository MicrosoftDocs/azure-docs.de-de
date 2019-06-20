---
title: 'NAT-Anforderungen für Verbindungen – ExpressRoute: Azure | Microsoft-Dokumentation'
description: Diese Seite enthält ausführliche Anforderungen für das Konfigurieren und Verwalten von NAT für ExpressRoute-Verbindungen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 508a20e826c2f7bf1260bde6858eb4472c16a5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883416"
---
# <a name="expressroute-nat-requirements"></a>NAT-Anforderungen für ExpressRoute
Zum Herstellen einer Verbindung mit Microsoft-Clouddiensten per ExpressRoute müssen Sie NATs einrichten und verwalten. Einige Konnektivitätsanbieter bieten das Einrichten und Verwalten von NAT als verwalteten Dienst an. Fragen Sie bei Ihrem Konnektivitätsanbieter nach, ob dieser Dienst angeboten wird. Wenn dies nicht der Fall ist, müssen Sie die unten beschriebenen Anforderungen erfüllen. 

Überprüfen Sie die Seite [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md) , um einen Überblick über die verschiedenen Routingdomänen zu erhalten. Um die Anforderungen für die öffentliche IP-Adresse für das öffentliche Azure- und Microsoft-Peering zu erfüllen, wird empfohlen, dass Sie NAT zwischen Ihrem Netzwerk und Microsoft einrichten. Dieser Abschnitt enthält eine ausführliche Beschreibung der NAT-Infrastruktur, die Sie einrichten möchten.

## <a name="nat-requirements-for-microsoft-peering"></a>NAT-Anforderungen für Microsoft-Peering
Mit dem Microsoft-Peeringpfad können Sie eine Verbindung mit den Clouddiensten von Microsoft herstellen, die vom öffentlichen Azure-Peeringpfad nicht unterstützt werden. Die Liste der Dienste umfasst Office 365-Dienste wie z.B. Exchange Online, SharePoint Online, Skype for Business und Dynamics 365. Microsoft wird die bidirektionale Konnektivität beim Microsoft-Peering voraussichtlich unterstützen. Für Datenverkehr, der auf Microsoft-Clouddiensten über öffentliches Peering abzielt, muss vor dem Eintritt in das Microsoft-Netzwerk SNAT mit gültigen, öffentlichen IPv4-Adressen angewendet werden. Auf Datenverkehr, der aus Microsoft Cloud Services in Ihr Netzwerk fließt, muss an der Internetgrenze SNAT angewendet werden, um [asymmetrisches Routing](expressroute-asymmetric-routing.md) zu verhindern. Die folgende Abbildung bietet einen allgemeinen Überblick über die Einrichtung von NAT für Microsoft-Peering.

![](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Datenverkehr von Ihrem Netzwerk an Microsoft
* Sie müssen sicherstellen, dass der Datenverkehr in den Microsoft-Peeringpfad über eine gültige, öffentliche IPv4-Adresse eintritt. Microsoft muss den Besitzer des IPv4-NAT-Adresspools gegen das regionale Routing Internet Registry (RIR) oder ein Internet Routing Registry (IRR) validieren können. Eine Überprüfung wird basierend auf der AS-Nummer ausgeführt, die für das Peering verwendet wird, und auf den für die NAT verwendeten IP-Adressen. Weitere Informationen zu Routingregistrierungen finden Sie auf der Seite [ExpressRoute-Routinganforderungen](expressroute-routing.md) .
* IP-Adressen, die für die Einrichtung des öffentlichen Azure-Peerings und andere ExpressRoute-Verbindungen verwendet werden, müssen Microsoft nicht über die BGP-Sitzung angekündigt werden. Es gibt keine Einschränkung für die Länge des Präfixes für die NAT-IP, die über dieses Peering angekündigt wird.
  
  > [!IMPORTANT]
  > Der Microsoft angekündigte NAT-IP-Adresspool muss nicht im Internet angekündigt werden. Ansonsten wird die Verbindung mit anderen Microsoft-Diensten unterbrochen.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Datenverkehr von Microsoft an Ihr Netzwerk
* Bestimmte Szenarios erfordern, dass Microsoft die Konnektivität zu den Dienstendpunkten innerhalb des Netzwerks initiiert. Ein typisches Szenarios wäre die Konnektivität zum ADFS-Server in Ihrem Netzwerk über Office 365. In solchen Fällen müssen Sie entsprechenden Präfixe aus dem Netzwerk in der Microsoft peering einfließen. 
* Sie müssen an der Internetgrenze SNAT auf den Microsoft-Datenverkehr für Dienstendpunkte in Ihrem Netzwerk anwenden, um [asymmetrisches Routing](expressroute-asymmetric-routing.md) zu verhindern. Anforderungen **und Antworten** mit einer Ziel-IP, die einer über ExpressRoute empfangenen Route entspricht, werden immer über ExpressRoute gesendet. Asymmetrische Routing tritt auf, wenn die Anforderung über das Internet empfangen und die Antwort über ExpressRoute gesendet wird. Wenn Sie an der Internetgrenze SNAT auf den eingehenden Microsoft-Verkehr anwenden, wird der Antwortdatenverkehr zurück an die Internetgrenze gezwungen. Dadurch wird das Problem behoben.

![Asymmetrisches Routing mit ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>NAT-Anforderungen für öffentliches Azure-Peering

> [!NOTE]
> Öffentliches Azure-Peering ist für neue Verbindungen nicht verfügbar.
> 

Der öffentliche Azure-Peeringpfad ermöglicht, dass Sie zu allen in Azure gehosteten Diensten über die öffentliche IP-Adresse eine Verbindung herstellen können. Dazu zählen Dienste, die unter [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md) aufgeführt sind und die von ISVs auf Microsoft Azure gehostet werden. 

> [!IMPORTANT]
> Die Konnektivität mit Microsoft Azure-Diensten für öffentliches Peering wird immer von Ihrem Netzwerk aus in das Microsoft-Netzwerk initiiert. Aus diesem Grund können für Microsoft Azure-Dienste per ExpressRoute keine Sitzungen mit Ihrem Netzwerk initiiert werden. Wenn dies versucht wird, wird für Pakete, die an diese angekündigten IP-Adressen gesendet werden, anstelle von ExpressRoute die Internetverbindung verwendet.
> 

Für Datenverkehr, der auf Microsoft Azure über öffentliches Peering abzielt, muss vor dem Eintritt in das Microsoft-Netzwerk SNAT mit gültigen, öffentlichen IPv4-Adressen angewendet werden. Die folgende Abbildung bietet einen allgemeinen Überblick über die Einrichtung von NAT, um die oben genannte Anforderung zu erfüllen.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT-IP-Pool und Routenankündigungen
Sie müssen sicherstellen, dass der Datenverkehr in den öffentlichen Azure-Peeringpfad über eine gültige, öffentliche IPv4-Adresse eintritt. Microsoft muss den Besitz des IPv4-NAT-Adresspools gegen ein regionales Routing Internet Registry (RIR) oder eine Internet Routing Registry (IRR) validieren können. Eine Überprüfung wird basierend auf der AS-Nummer ausgeführt, die für das Peering verwendet wird, und auf den für die NAT verwendeten IP-Adressen. Weitere Informationen zu Routingregistrierungen finden Sie auf der Seite [ExpressRoute-Routinganforderungen](expressroute-routing.md) .

Es gibt keine Einschränkungen für die Länge des Präfixes für die NAT-IP, die über dieses Peering angekündigt wird. Sie müssen den NAT-Pool überwachen und sicherstellen, dass Sie die NAT-Sitzungen nicht außer acht lassen.

> [!IMPORTANT]
> Der Microsoft angekündigte NAT-IP-Adresspool muss nicht im Internet angekündigt werden. Ansonsten wird die Verbindung mit anderen Microsoft-Diensten unterbrochen.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
* Sehen Sie sich die Anforderungen für [Routing](expressroute-routing.md) und [QoS](expressroute-qos.md) an.
* Workflowinformationen finden Sie unter [Bereitstellungsworkflows für ExpressRoute-Verbindungen und Verbindungszustände](expressroute-workflows.md).
* Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  
  * [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurieren des Routings](expressroute-howto-routing-portal-resource-manager.md)
  * [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-portal-resource-manager.md)

