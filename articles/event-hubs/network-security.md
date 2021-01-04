---
title: Netzwerksicherheit für Azure Event Hubs
description: In diesem Artikel wird beschrieben, wie Sie Zugriff über private Endpunkte konfigurieren.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 9503fc26c22d7dbff13c5754288f577b7bb3242f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010995"
---
# <a name="network-security-for-azure-event-hubs"></a>Netzwerksicherheit für Azure Event Hubs 
In diesem Artikel wird beschrieben, wie Sie die folgenden Sicherheitsfunktionen mit Azure Event Hubs verwenden: 

- Diensttags
- IP-Firewallregeln
- Netzwerkdienstendpunkte
- Private Endpunkte


## <a name="service-tags"></a>Diensttags
Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Tag automatisch, wenn sich die Adressen ändern. Auf diese Weise wird die Komplexität häufiger Updates an Netzwerksicherheitsregeln minimiert. Weitere Informationen zu Diensttags finden Sie unter [Diensttags: Übersicht](../virtual-network/service-tags-overview.md).

Sie können Diensttags verwenden, um Netzwerkzugriffssteuerungen in [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) oder in der [Azure Firewall](../firewall/service-tags.md) zu definieren. Verwenden Sie Diensttags anstelle von spezifischen IP-Adressen, wenn Sie Sicherheitsregeln erstellen. Durch Angeben des Diensttagnamens (z. B. **EventHub**) im entsprechenden *Quell*- oder *Ziel* feld einer Regel können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern.

| Diensttag | Zweck | Eingehend oder ausgehend möglich? | Regional möglich? | Einsatz mit Azure Firewall möglich? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | Ausgehend | Ja | Ja |


## <a name="ip-firewall"></a>IP-Firewall 
Standardmäßig kann über das Internet auf Event Hubs-Namespaces zugegriffen werden, solange die Anforderung eine gültige Authentifizierung und Autorisierung aufweist. Mit der IP-Firewall können Sie den Zugriff auf eine Gruppe von IPv4-Adressen oder IPv4-Adressbereichen in der [CIDR-Notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) weiter einschränken.

Diese Funktion ist in Szenarien hilfreich, in denen Azure Event Hubs nur von bestimmten bekannten Websites aus zugänglich sein soll. Mithilfe von Firewallregeln können Sie Regeln konfigurieren, um Datenverkehr von bestimmten IPv4-Adressen zuzulassen. Wenn Sie z. B. Event Hubs mit [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) verwenden, können Sie eine **Firewallregel** erstellen, um Datenverkehr nur von den IP-Adressen Ihrer lokalen Infrastruktur zuzulassen. 

Die IP-Firewallregeln werden auf der Event Hubs-Namespaceebene angewendet. Daher gelten die Regeln für alle Clientverbindungen mit einem beliebigen unterstützten Protokoll. Jeder Verbindungsversuch von einer IP-Adresse, die nicht mit einer zulässigen IP-Regel im Event Hubs-Namespace übereinstimmt, wird als nicht autorisiert abgelehnt. In der Antwort wird die IP-Regel nicht erwähnt. IP-Filterregeln werden der Reihe nach angewendet, und die erste Regel, die eine Übereinstimmung mit der IP-Adresse ergibt, bestimmt die Aktion (Zulassen oder Ablehnen).

Weitere Informationen finden Sie unter [Konfigurieren einer IP-Firewall für einen Event Hub](event-hubs-ip-filtering.md).

## <a name="network-service-endpoints"></a>Netzwerkdienstendpunkte
Die Integration von Event Hubs und [VNET-Dienstendpunkten](../virtual-network/virtual-network-service-endpoints-overview.md) ermöglicht den sicheren Zugriff auf Messagingfunktionen für Workloads, z. B. an virtuelle Netzwerke (VNETs) gebundene virtuelle Computer, wobei der Pfad für den Netzwerkdatenverkehr an beiden Enden geschützt ist.

Nachdem die Konfiguration der Bindung an mindestens einen Dienstendpunkt des VNET-Subnetzes durchgeführt wurde, akzeptiert der entsprechende Event Hubs-Namespace nur noch Datenverkehr von autorisierten Subnetzen in virtuellen Netzwerken. Aus Sicht des virtuellen Netzwerks wird durch die Bindung eines Event Hubs-Namespace an einen Dienstendpunkt ein isolierter Netzwerktunnel vom Subnetz des virtuellen Netzwerks zum Messagingdienst konfiguriert. 

Das Ergebnis ist eine private und isolierte Beziehung zwischen den Workloads, die an das Subnetz gebunden sind, und dem entsprechenden Event Hubs-Namespace, obwohl sich die beobachtbare Netzwerkadresse des Messaging-Dienstendpunkts in einem öffentlichen IP-Bereich befindet. Es gibt bei diesem Verhalten eine Ausnahme. Durch die Aktivierung eines Dienstendpunkts wird standardmäßig die `denyall`-Regel in der [IP-Firewall](event-hubs-ip-filtering.md) aktiviert, die dem virtuellen Netzwerk zugeordnet ist. Sie können bestimmte IP-Adressen in der IP-Firewall hinzufügen, um den Zugriff auf den öffentlichen Endpunkt des Event Hub zu ermöglichen. 

> [!IMPORTANT]
> Virtuelle Netzwerke werden in den Tarifen **Standard** und **Dediziert** von Event Hubs unterstützt. Im **Basic**-Tarif werden sie nicht unterstützt.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Erweiterte Sicherheitsszenarien basierend auf der VNET-Integration 

Lösungen, für die eine strikte und auf mehrere Bereiche aufgeteilte Sicherheit erforderlich ist und bei denen die VNET-Subnetze die Segmentierung zwischen den einzelnen aufgeteilten Diensten bereitstellen, benötigen weiterhin Kommunikationspfade zwischen den Diensten, die sich in diesen Bereichen befinden.

Für alle direkten IP-Routen zwischen den Bereichen (auch für HTTPS per TCP/IP) besteht die Gefahr, dass ab der Vermittlungsschicht Sicherheitsrisiken ausgenutzt werden. Bei Messagingdiensten werden isolierte Kommunikationspfade bereitgestellt, für die Nachrichten während des Übergangs zwischen Parteien sogar auf Datenträger geschrieben werden. Workloads in zwei einzelnen virtuellen Netzwerken, die beide an dieselbe Event Hubs-Instanz gebunden sind, können über Nachrichten effizient und zuverlässig kommunizieren, während die Integrität der jeweiligen Netzwerkisolationsgrenze aufrechterhalten wird.
 
Dies bedeutet, dass Ihre sicherheitsrelevanten Cloudlösungen nicht nur Zugriff auf zuverlässige und skalierbare Azure-Funktionen für asynchrones Messaging eines Branchenführers haben, sondern dass das Messaging jetzt auch genutzt werden kann, um Kommunikationspfade zwischen sicheren Lösungsbereichen zu erstellen. Diese sind deutlich sicherer als alle Optionen des Peer-to-Peer-Kommunikationsmodus, einschließlich HTTPS und andere per TLS geschützte Socketprotokolle.

### <a name="bind-event-hubs-to-virtual-networks"></a>Binden von Event Hubs an virtuelle Netzwerke

**VNET-Regeln** sind ein Feature für die Firewallsicherheit, mit dem gesteuert wird, ob Ihr Azure Event Hubs-Namespace Verbindungen eines bestimmten VNET-Subnetzes akzeptiert.

Das Binden eines Event Hubs-Namespace an ein virtuelles Netzwerk ist ein Prozess mit zwei Schritten. Zuerst müssen Sie einen **VNET-Dienstendpunkt** in einem Subnetz eines virtuellen Netzwerks erstellen und für **Microsoft.EventHub** aktivieren, wie im Artikel [Übersicht über Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) beschrieben. Nachdem Sie den Dienstendpunkt hinzugefügt haben, binden Sie den Event Hubs-Namespace mit einer **VNET-Regel** daran.

Die VNET-Regel ist eine Zuordnung des Event Hubs-Namespace zu einem Subnetz eines virtuellen Netzwerks. Während die Regel vorhanden ist, wird allen Workloads, die an das Subnetz gebunden sind, Zugriff auf den Event Hubs-Namespace gewährt. Event Hubs selbst stellt niemals ausgehende Verbindungen her, muss keinen Zugriff erhalten und erhält daher niemals die Gewährung des Zugriffs auf Ihr Subnetz, indem diese Regel aktiviert wird.

Weitere Informationen finden Sie unter [Konfigurieren von Dienstendpunkten eines virtuellen Netzwerks für einen Event Hub](event-hubs-service-endpoints.md).

## <a name="private-endpoints"></a>Private Endpunkte

Mit dem [Azure Private Link-Dienst](../private-link/private-link-overview.md) können Sie über einen **privaten Endpunkt** in Ihrem virtuellen Netzwerk auf Azure-Dienste wie Azure Event Hubs, Azure Storage und Azure Cosmos DB sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.

Ein privater Endpunkt ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

> [!IMPORTANT]
> Diese Funktion wird sowohl für den Tarif **Standard** als auch für den Tarif **Dedicated** unterstützt. Im **Basic**-Tarif werden sie nicht unterstützt.

Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte für einen Event Hub](private-link-service.md).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Konfigurieren einer IP-Firewall für einen Event Hub](event-hubs-ip-filtering.md)
- [Konfigurieren von Dienstendpunkten eines virtuellen Netzwerks für einen Event Hub](event-hubs-service-endpoints.md)
- [Konfigurieren von privaten Endpunkten für einen Event Hub](private-link-service.md)