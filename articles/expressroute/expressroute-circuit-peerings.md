---
title: Azure ExpressRoute-Verbindungen und Peering | Microsoft-Dokumentation
description: Diese Seite enthält eine Übersicht über ExpressRoute-Verbindungen und Routingdomänen/Peering.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: c4290473a7c1edce02d74a4a787c62ccf0d9c052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924306"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-Verbindungen und Peering

Über ExpressRoute-Verbindungen wird Ihre lokale Infrastruktur über einen Konnektivitätsanbieter mit Microsoft verbunden. In diesem Artikel werden ExpressRoute-Verbindungen und Routingdomänen/Peering erläutert. Die folgende Abbildung zeigt eine logische Darstellung der Konnektivität zwischen Ihrem WAN und Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Öffentliches Azure-Peering wurde als veraltet markiert und ist für neue ExpressRoute-Leitungen nicht verfügbar. Neue Leitungen unterstützen Microsoft-Peering und privates Peering.  
>

## <a name="circuits"></a>ExpressRoute-Verbindungen

Eine ExpressRoute-Verbindung stellt eine logische Verbindung zwischen der lokalen Infrastruktur und den Microsoft-Clouddiensten über einen Konnektivitätsanbieter dar. Sie können mehrere ExpressRoute-Verbindungen bestellen. Alle Verbindungen können sich in derselben Region oder in verschiedenen Regionen befinden und über verschiedene Konnektivitätsanbieter mit dem jeweiligen Standort verbunden sein.

ExpressRoute-Verbindungen werden keinen physischen Entitäten zugeordnet. Eine Verbindung wird eindeutig durch eine Standard-GUID, einen sogenannten Dienstschlüssel, identifiziert. Der Dienstschlüssel ist die einzige Information, die zwischen Microsoft, dem Konnektivitätsanbieter und Ihnen ausgetauscht wird. Der Dienstschlüssel unterliegt aus Sicherheitsgründen nicht der Geheimhaltung. Zwischen einer ExpressRoute-Verbindung und dem Dienstschlüssel besteht eine 1:1-Zuordnung.

Neue ExpressRoute-Verbindungen können zwei unabhängige Peerings aufweisen: privates Peering und Microsoft-Peering. Bestehende ExpressRoute-Verbindungen können im Gegensatz dazu bis zu drei unabhängige Peerings aufweisen: öffentliches Azure-Peering, privates Azure-Peering und Microsoft-Peering. Jedes Peering besteht aus einem Paar unabhängiger BGP-Sitzungen, die für Hochverfügbarkeit jeweils redundant konfiguriert sind. Zwischen einer ExpressRoute-Verbindung und Routingdomänen besteht eine 1:N-Zuordnung (1 <= N <= 3). Für eine ExpressRoute-Verbindung können einzelne, zwei oder alle drei Peerings pro ExpressRoute-Verbindung aktiviert sein.

Jede Verbindung verfügt über eine feste Bandbreite (50 MBit/s, 100 MBit/s, 200 MBit/s, 500 MBit/s, 1 GBit/s, 10 GBit/s) und ist einem Konnektivitätsanbieter und einem Peeringstandort zugewiesen. Die ausgewählte Bandbreite wird von allen Peerings der Verbindung gemeinsam genutzt.

### <a name="quotas"></a>Kontingente, Grenzwerte und Einschränkungen

Standardkontingente und -grenzwerte gelten für alle ExpressRoute-Verbindungen. Aktuelle Informationen zu Kontingenten finden Sie auf der Seite [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](../azure-subscription-service-limits.md) .

## <a name="routingdomains"></a>ExpressRoute-Peering

Einer ExpressRoute-Verbindung sind mehrere Routingdomänen/Peerings zugeordnet: öffentliche oder private Azure-Routingdomänen/-Peerings sowie Microsoft-Routingdomänen/-Peerings. Alle Peerings sind für Hochverfügbarkeit auf einem Routerpaar identisch konfiguriert (mit einer Aktiv/Aktiv-Konfiguration oder einer Nutzlastverteilungskonfiguration). Azure-Dienste sind zur Darstellung der IP-Adressierungsschemas als *Azure – Öffentlich* und *Azure – Privat* kategorisiert.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Privates Azure-Peering:

Azure-Computedienste, sprich virtuelle Computer (IaaS) und Clouddienste (PaaS), die in einem virtuellen Netzwerk bereitgestellt werden, können über die private Peeringdomäne verbunden werden. Die private Peeringdomäne gilt als vertrauenswürdige Erweiterung Ihres Kernnetzwerks für Microsoft Azure. Sie können eine bidirektionale Verbindung zwischen Ihrem Kernnetzwerk und den virtuellen Azure-Netzwerken (VNets) einrichten. Durch dieses Peering können Sie direkt über ihre privaten IP-Adressen eine Verbindung zwischen virtuellen Computern und Clouddiensten herstellen.  

Sie können mehr als ein virtuelles Netzwerk mit der privaten Peeringdomäne verbinden. Informationen zu Grenzwerten und Einschränkungen finden Sie auf der [FAQ-Seite](expressroute-faqs.md) . Aktuelle Informationen zu Grenzwerten finden Sie auf der Seite [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](../azure-subscription-service-limits.md) .  Ausführliche Informationen zur Routingkonfiguration finden Sie unter [Routing](expressroute-routing.md) .

### <a name="microsoftpeering"></a>Microsoft-Peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Verbindungen mit Onlinediensten von Microsoft (Office 365, Dynamics 365 und Azure-PaaS-Dienste) erfolgen per Microsoft-Peering. Über die Peeringrouting-Domäne von Microsoft ermöglichen wir eine bidirektionale Konnektivität zwischen Ihrem WAN und den Microsoft-Clouddiensten. Sie dürfen nur über öffentliche IP-Adressen, die Ihnen oder Ihrem Konnektivitätsanbieter gehören, eine Verbindung mit den Microsoft-Clouddiensten herstellen und müssen alle definierten Regeln einhalten. Weitere Informationen finden Sie auf der Seite [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

Auf der [FAQ-Seite](expressroute-faqs.md) finden Sie weitere Informationen zu unterstützten Diensten, Kosten und Konfigurationsdetails. Auf der Seite [ExpressRoute-Standorte](expressroute-locations.md) finden Sie Informationen zur Liste der Konnektivitätsanbieter, die Microsoft-Peeringsupport anbieten.

### <a name="publicpeering"></a>Öffentliches Azure-Peering (für neue Verbindungen veraltet)

> [!Note]
> Im öffentlichen Azure-Peering ist jeder BGP-Sitzung eine NAT-IP-Adresse zugeordnet. Wechseln Sie bei mehr als 2 NAT-IP-Adressen zu Microsoft-Peering. Microsoft-Peering ermöglicht Ihnen, Ihre eigenen NAT-Zuordnungen zu konfigurieren und Routenfilter für selektive Präfixankündigungen zu verwenden. Weitere Informationen finden Sie unter [Wechseln zu Microsoft-Peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

Dienste wie Azure Storage, SQL-Datenbanken und Websites werden über öffentliche IP-Adressen angeboten. Über die öffentliche Peeringrouting-Domäne können Sie eine private Verbindung mit unter öffentlichen IP-Adressen gehosteten Diensten herstellen (darunter VIPs Ihrer Clouddienste). Sie können die öffentliche Peeringdomäne mit der DMZ verbinden und aus Ihrem WAN heraus eine Verbindung mit allen Azure-Diensten unter ihren öffentlichen IP-Adressen herstellen, ohne eine Verbindung über das Internet herstellen zu müssen.

Die Verbindung wird immer von Ihrem WAN aus mit den Microsoft Azure-Diensten initiiert. Microsoft Azure-Dienste können über diese Routingdomäne keine Verbindungen in Ihr Netzwerk initiieren. Sobald öffentliches Peering aktiviert ist, werden Sie eine Verbindung mit allen Azure-Diensten herstellen können. Es ist Ihnen nicht gestattet, selektiv Dienste auszuwählen, zu denen wir Routen ankündigen.

Innerhalb des Netzwerks können Sie benutzerdefinierte Routingfilter definieren, um nur die benötigten Routen zu nutzen. Ausführliche Informationen zur Routingkonfiguration finden Sie unter [Routing](expressroute-routing.md) .

In den [häufig gestellten Fragen](expressroute-faqs.md) finden Sie weitere Informationen zu Diensten, die von der öffentlichen Peeringrouting-Domäne unterstützt werden.

## <a name="peeringcompare"></a>Peeringvergleich

In der folgenden Tabelle werden die drei Peerings verglichen:

|  | **Privates Peering** | **Microsoft-Peering** |  **Öffentliches Peering** (für neue Verbindungen veraltet) |
| --- | --- | --- | --- |
| **Max. Anzahl von unterstützten Präfixen pro Peering** |Standardmäßig 4.000, bei ExpressRoute Premium 10.000 |200 |200 |
| **Unterstützte IP-Adressbereiche** |Jede gültige IPv4-Adresse innerhalb des WAN |Öffentliche IP-Adressen in Ihrem Besitz oder im Besitz des Konnektivitätsanbieters |Öffentliche IP-Adressen in Ihrem Besitz oder im Besitz des Konnektivitätsanbieters |
| **Anforderungen für AS-Nummern** |Private und öffentliche AS-Nummern. Wenn Sie eine öffentliche AS-Nummer verwenden möchten, muss diese in Ihrem Besitz sein. |Private und öffentliche AS-Nummern. Sie müssen allerdings den Besitz öffentlicher IP-Adressen nachweisen. |Private und öffentliche AS-Nummern. Sie müssen allerdings den Besitz öffentlicher IP-Adressen nachweisen. |
| **Unterstützte IP-Protokolle**| IPv4 |  IPv4, IPv6 | IPv4 |
| **IP-Adressen der Routingschnittstelle** |RFC1918 und öffentliche IP-Adressen |In Routingregistrierungen für Sie registrierte öffentliche IP-Adressen. |In Routingregistrierungen für Sie registrierte öffentliche IP-Adressen. |
| **MD5-Hash-Unterstützung** |Ja |Ja |Ja |

Sie können mehrere Routingdomänen als Teil der ExpressRoute-Verbindung aktivieren. Sie können alle Routingdomänen durch das gleiche VPN leiten, wenn sie diese zu einer einzelnen Routingdomäne zusammenführen möchten. Sie können sie auch getrennt halten, ähnlich wie im Diagramm. Die empfohlene Konfiguration sieht folgendermaßen aus: Das private Peering ist direkt mit dem Kernnetzwerk verbunden, und die öffentlichen Peeringlinks und die Microsoft-Peeringlinks sind mit Ihrer DMZ verbunden.

Jedes Peering erfordert separate BGP-Sitzungen (ein Sitzungspaar für jeden Peeringtyp). Die BGP-Sitzungspaare bieten einen hoch verfügbaren Link. Wenn Sie die Verbindung über Layer-2-Konnektivitätsanbieter herstellen, sind Sie für das Konfigurieren und Verwalten des Routings verantwortlich. Weitere Informationen erhalten Sie, wenn Sie sich [Workflows](expressroute-workflows.md) zum Einrichten von ExpressRoute anschauen.

## <a name="health"></a>ExpressRoute-Integrität

ExpressRoute-Leitungen können mit dem [Netzwerkleistungsmonitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) auf Verfügbarkeit, Konnektivität zu VNETs und Bandbreitennutzung überwacht werden.

Der Netzwerkleistungsmonitor überwacht die Integrität von privatem Azure-Peering und Microsoft-Peering. Weitere Informationen finden Sie in unserem [Blogbeitrag](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/).

## <a name="next-steps"></a>Nächste Schritte

* Suchen Sie nach einem Service Provider. Informationen finden Sie unter [ExpressRoute-Dienstanbieter und -Standorte](expressroute-locations.md).
* Stellen Sie sicher, dass alle Voraussetzungen erfüllt sind. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).
* Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  * [Erstellen und Verwalten von ExpressRoute-Verbindungen](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurieren des Routings (Peering) für ExpressRoute-Verbindungen](expressroute-howto-routing-portal-resource-manager.md)
