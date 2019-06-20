---
title: Sicherer Zugriff auf ein Azure Cosmos DB-Konto durch Verwenden eines Azure Virtual Network-Dienstendpunkts
description: Dieses Dokument beschreibt die Zugangssteuerung für virtuelle Netzwerke und Subnetze für ein Azure Cosmos DB-Konto.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: dfc3ebc0274c87466d6dc27c93880483df023085
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242471"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Zugreifen auf Azure Cosmos DB über virtuelle Netzwerke (VNET)

Sie können das Azure Cosmos DB-Konto so konfigurieren, dass der Zugriff nur aus einem bestimmten Subnetz des virtuellen Netzwerks (VNET) zugelassen wird. Wenn der [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) aktiviert wird, um auf Azure Cosmos DB im Subnetz innerhalb eines virtuellen Netzwerks zuzugreifen, wird der Datenverkehr dieses Subnetzes mit der Identität des Subnetzes und des virtuellen Netzwerks an Azure Cosmos DB gesendet. Sobald der Azure Cosmos DB-Dienstendpunkt aktiviert ist, können Sie den Zugriff auf das Subnetz einschränken, indem Sie es Ihrem Azure Cosmos DB-Konto hinzufügen.

Standardmäßig ist ein Azure Cosmos DB-Konto über jede beliebige Quelle zugänglich, wenn die Anforderung zusammen mit einem gültigen Autorisierungstoken erfolgt. Wenn Sie mindestens ein Subnetz im VNET hinzufügen, erhalten nur Anforderungen aus dem jeweiligen Subnetz eine gültige Antwort. Anforderungen, die aus anderen Quellen stammen, erhalten die Fehlermeldung „403“ (Nicht zulässig). 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Hier sind einige häufig gestellte Fragen zur Konfiguration des Zugriffs aus virtuellen Netzwerken:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kann ich sowohl den VNET-Dienstendpunkt als auch eine IP-Zugriffssteuerungsrichtlinie für ein Azure Cosmos DB-Konto festlegen? 

Sie können sowohl den VNET-Dienstendpunkt als auch eine IP-Zugriffssteuerungsrichtlinie (alias Firewall) für Ihr Azure Cosmos DB-Konto aktivieren. Diese beiden Funktionen ergänzen sich und gewährleisten zusammen die Isolation und Sicherheit Ihres Azure Cosmos DB-Kontos. Die Verwendung einer IP-Firewall stellt sicher, dass statische IP-Adressen auf Ihr Konto zugreifen können. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Wie schränke ich den Zugriff auf ein Subnetz innerhalb eines virtuellen Netzwerks ein? 

Um den Zugriff auf das Azure Cosmos DB-Konto über ein Subnetz einzuschränken, müssen Sie zwei Schritte ausführen. Lassen Sie zunächst zu, dass der Datenverkehr aus dem Subnetz seine Subnetz- und virtuelle Netzwerkidentität in Azure Cosmos DB überträgt. Aktivieren Sie dazu den Dienstendpunkt für Azure Cosmos DB im Subnetz. Fügen Sie als Nächstes im Azure Cosmos DB-Konto eine Regel hinzu, die dieses Subnetz als eine Quelle für den Zugriff auf das Konto angibt.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Werden Zugriffssteuerungslisten virtueller Netzwerke und die IP-Firewall Anforderungen oder Verbindungen zurückgewiesen? 

Wenn eine IP-Firewall oder Zugriffsregeln für virtuelle Netzwerke hinzugefügt werden, erhalten nur Anforderungen von zulässigen Quellen gültige Antworten. Andere Anforderungen werden mit einer „403“-Fehlermeldung (Nicht zulässig) zurückgewiesen. Es ist wichtig, die Firewall des Azure Cosmos DB-Kontos von einer Firewall auf Verbindungsebene zu unterscheiden. Die Quelle kann sich weiterhin mit dem Dienst verbinden, und die Verbindungen selbst werden nicht zurückgewiesen.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Meine Anforderungen werden blockiert, wenn ich den Dienstendpunkt für Azure Cosmos DB im Subnetz aktiviere. Was ist passiert?

Wenn der Dienstendpunkt für Azure Cosmos DB in einem Subnetz aktiviert ist, wechselt die Quelle des Datenverkehrs, der das Konto erreicht, von der öffentlichen IP-Adresse zu einem virtuellen Netzwerk und Subnetz. Wenn Ihr Azure Cosmos DB-Konto über eine einzige IP-basierte Firewall verfügt, würde der Datenverkehr vom im Dienst aktivierten Subnetz nicht mehr den IP-Firewallregeln entsprechen und daher zurückgewiesen werden. Befolgen Sie die Anweisungen zur nahtlosen Migration von einer IP-basierten Firewall zu einer Zugriffssteuerung, die auf einem virtuellen Netzwerk basiert.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Haben die virtuellen Netzwerke mit Peering auch Zugriff auf das Azure Cosmos DB-Konto? 
Nur virtuelle Netzwerke und ihre Subnetze, die dem Azure Cosmos DB-Konto hinzugefügt wurden, haben Zugriff. Ihre VNETs mit Peering können erst auf das Konto zugreifen, wenn die Subnetze in den virtuellen Netzwerken mit Peering dem Konto hinzugefügt wurden.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Wie viele Subnetze dürfen maximal auf ein einzelnes Cosmos DB-Konto zugreifen? 
Derzeit können Sie maximal 64 Subnetze für ein Azure Cosmos DB-Konto zulassen.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kann ich den Zugriff über VPN und ExpressRoute aktivieren? 
Für den Zugriff auf das Azure Cosmos DB-Konto über eine lokale ExpressRoute-Instanz müssen Sie Microsoft-Peering aktivieren. Wenn Sie eine IP-Firewall oder Zugriffsregeln für das virtuelle Netzwerk eingerichtet haben, können Sie die öffentlichen IP-Adressen, die für das Microsoft-Peering verwendet werden, der IP-Firewall Ihres Azure Cosmos DB-Kontos hinzufügen, um lokalen Diensten Zugriff auf das Azure Cosmos DB-Konto zu gewähren. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Muss ich die Regeln der Netzwerksicherheitsgruppen (NSG) aktualisieren? 
NSG-Regeln werden verwendet, um die Konnektivität mit einem Subnetz mit virtuellem Netzwerk einzuschränken. Wenn Sie den Dienstendpunkt für Azure Cosmos DB dem Subnetz hinzufügen, muss die ausgehende Konnektivität in NSG nicht für Ihr Azure Cosmos DB-Konto geöffnet werden. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Sind Dienstendpunkte für alle VNETs verfügbar?
Nein, nur virtuelle Netzwerke mit Azure Resource Manager können einen Dienstendpunkt aktivieren. Klassische virtuelle Netzwerke unterstützen keine Dienstendpunkte.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kann ich „Verbindungen aus öffentlichen Azure-Rechenzentren akzeptieren“, wenn der Zugriff auf den Dienstendpunkt für Azure Cosmos DB aktiviert ist?  
Dies ist nur erforderlich, wenn Sie den Zugriff auf Ihr Azure Cosmos DB-Konto über andere Azure-Erstanbieterdienste wie Azure Data Factory, Azure Search oder einen beliebigen anderen Dienst ermöglichen möchten, der in einer bestimmten Azure-Region bereitgestellt wurde.


## <a name="next-steps"></a>Nächste Schritte

* [Einschränken des Azure Cosmos DB-Kontozugriffs aus Subnetzen in virtuellen Netzwerken](how-to-configure-vnet-service-endpoint.md)
* [Konfigurieren der IP-Firewall für Ihr Azure Cosmos DB-Konto](how-to-configure-firewall.md)

