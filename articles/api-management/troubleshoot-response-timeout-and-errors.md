---
title: Problembehandlung bei Timeouts und Fehlern bei Clientantworten in API Management
description: Beheben von zeitweiligen Verbindungsfehlern und zugehörigen Latenzproblemen in API Management
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 770a8191b1b07a7ebc779b84f443ae96d66d1c97
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96839679"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Problembehandlung bei Timeouts und Fehlern bei Clientantworten in API Management

In diesem Artikel erfahren Sie, wie Sie zeitweilige Verbindungsfehler und zugehörige Latenzprobleme in [Azure API Management](./api-management-key-concepts.md) beheben. Dieser Artikel enthält insbesondere Informationen und Lösungen zur Problembehandlung für die Auslastung von SNAT-Ports (Source Network Address Translation). Wenn Sie weitere Unterstützung benötigen, wenden Sie sich unter [Azure-Communitysupport](https://azure.microsoft.com/support/community/) an Azure-Experten, oder senden Sie eine Supportanfrage an den [Azure-Support](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Symptome

Clientanwendungen, die APIs über Ihren API Management-Dienst (APIM) aufrufen, weisen möglicherweise eines oder mehrere der folgenden Symptome auf:

* Zeitweilige HTTP 500-Fehler
* Timeoutfehlermeldungen

Diese Symptome werden als Instanzen von `BackendConnectionFailure` in [Azure Monitor-Ressourcenprotokollen](../azure-monitor/platform/resource-logs.md) angegeben.

## <a name="cause"></a>Ursache

Dieses Systemmuster tritt häufig aufgrund von SNAT-Portlimits (Source Network Address Translation) in Ihrem APIM-Dienst auf.

Wenn ein Client eine Ihrer APIM-APIs aufruft, öffnet der Azure API Management-Dienst einen SNAT-Port für den Zugriff auf die Back-End-API. Wie unter [Ausgehende Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md) erläutert, verwendet Azure die Quell-Netzwerkadressenübersetzung (Source Network Address Translation, SNAT) und einen Load Balancer (nicht für Kunden verfügbar gemacht) für die Kommunikation mit Endpunkten außerhalb von Azure im öffentlichen IP-Adressraum sowie mit internen Endpunkten in Azure, die keine [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) verwenden. Dieser Fall gilt nur für Back-End-APIs, die über öffentliche IP-Adressen verfügbar gemacht werden.

Jeder Instanz des API Management-Diensts wird zunächst eine vorab zugeordnete Anzahl von SNAT-Ports zugewiesen. Dieses Limit wirkt sich auf das Öffnen von Verbindungen mit derselben Host- und Portkombination aus. Die SNAT-Ports werden bei wiederholten Aufrufen derselben Kombination aus Adresse und Port aufgebraucht. Nach der Freigabe eines SNAT-Ports kann er bei Bedarf wiederverwendet werden. Der Azure Network Load Balancer gibt SNAT-Ports von geschlossenen Verbindungen erst nach einer Wartezeit von vier Minuten frei.

Durch eine schnelle Abfolge von Clientanforderungen an die APIs kann das vorab zugeordnete Kontingent von SNAT-Ports ausgeschöpft werden, wenn diese Ports nicht schnell genug geschlossen und wiederverwendet werden. Dadurch wird verhindert, dass der APIM-Dienst Clientanforderungen zeitnah verarbeitet.

## <a name="mitigations-and-solutions"></a>Entschärfungen und Lösungen

Um das Problem der SNAT-Portauslastung zu beheben, muss zunächst die Leistung Ihrer Back-End-Dienste diagnostiziert und optimiert werden.

Allgemeine Strategien zum Verringern der SNAT-Portauslastung finden Sie unter [Problembehandlung für Fehler bei ausgehenden Verbindungen](../load-balancer/troubleshoot-outbound-connection.md) in der Dokumentation zu *Azure Load Balancer*. Dabei können die folgenden Strategien in API Management angewandt werden.

### <a name="scale-your-apim-instance"></a>Skalieren Ihrer APIM-Instanz

Jeder API Management-Instanz sind basierend auf den APIM-Einheiten mehrere SNAT-Ports zugeordnet. Sie können weitere SNAT-Ports zuordnen, indem Sie die API Management-Instanz mit zusätzlichen Einheiten skalieren. Weitere Informationen finden Sie unter [Skalieren Ihres API Management-Diensts](upgrade-and-scale.md#scale-your-api-management-service).

> [!NOTE]
> Die Verwendung von SNAT-Ports ist derzeit nicht als Metrik für die automatische Skalierung von API Management-Einheiten verfügbar.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Verwenden mehrerer IP-Adressen für die Back-End-URLs

Bei jeder Verbindung von Ihrer APIM-Instanz mit der gleichen Ziel-IP-Adresse und dem gleichen Zielport wie der Back-End-Dienst wird ein SNAT-Port verwendet, um einen eindeutigen Datenverkehrsfluss zu ermöglichen. Ohne unterschiedliche SNAT-Ports für den Antwortdatenverkehr vom Hintergrunddienst ist es in APIM nicht möglich, die einzelnen Antworten voneinander zu trennen.

Da SNAT-Ports wiederverwendet werden können, wenn sich die Ziel-IP-Adresse oder der Zielport unterscheiden, besteht eine weitere Möglichkeit zur Vermeidung der SNAT-Portauslastung darin, mehrere IP-Adressen für die URLs des Back-End-Diensts zu verwenden.

Weitere Informationen finden Sie unter [Azure Load Balancer als Proxy für ausgehenden Datenverkehr](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Platzieren des APIM-Diensts und Back-End-Diensts im gleichen VNET

Wenn die Back-End-API in einem Azure-Dienst gehostet wird, der *Dienstendpunkte* wie App Service unterstützt, können Sie Probleme mit der SNAT-Portauslastung vermeiden, indem Sie die APIM-Instanz und den Back-End-Dienst im gleichen virtuellen Netzwerk platzieren und dieses über [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) oder [private Endpunkte](../private-link/private-endpoint-overview.md) verfügbar machen. Wenn Sie ein gemeinsames VNET verwenden und Dienstendpunkte im Integrationssubnetz platzieren, umgeht der ausgehende Datenverkehr von der APIM-Instanz an diese Dienste das Internet, wodurch Einschränkungen von SNAT-Ports vermieden werden. Auch treten keine Probleme bei ausgehenden SNAT-Ports an dieses Ziel auf, wenn Sie ein VNET und private Endpunkte verwenden.

Weitere Informationen finden Sie unter [Verwenden von Azure API Management mit virtuellen Netzwerken](api-management-using-with-vnet.md) und [Integrieren von App Service in ein virtuelles Azure-Netzwerk](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Platzieren des APIM-Diensts in einem virtuellen Netzwerk und Weiterleiten von ausgehenden Aufrufen an Azure Firewall

Ähnlich wie bei der Platzierung des APIM-Diensts und Back-End-Diensts in einem virtuellen Netzwerk können Sie Azure Firewall in einem VNET mit dem APIM-Dienst verwenden und dann ausgehende APIM-Aufrufe an Azure Firewall weiterleiten. Zwischen APIM und Azure Firewall (im gleichen VNET) sind keine SNAT-Ports erforderlich. Für SNAT-Verbindungen mit Ihren Back-End-Diensten bietet Azure Firewall 64.000 verfügbare Ports, eine wesentlich höhere Anzahl als die für APIM-Instanzen zugeordneten Ports.

Weitere Informationen finden Sie in der Dokumentation zu [Azure Firewall](../firewall/overview.md).

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Zwischenspeichern von Antworten und andere Leistungsoptimierungen für Back-End-APIs

Eine weitere mögliche Abhilfemaßnahme ist das Verbessern der Verarbeitungszeiten für die Back-End-APIs. Eine Möglichkeit hierfür besteht darin, bestimmte APIs mit der Zwischenspeicherung von Antworten zu konfigurieren, um die Latenz zwischen Clientanwendungen, die Ihre API aufrufen, und die Back-End-Last der APIM-Instanz zu reduzieren.

Weitere Informationen finden Sie unter [Hinzufügen der Zwischenspeicherung zum Verbessern der Leistung in Azure API Management](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Implementieren von Richtlinien für die Zugriffsbeschränkung

Wenn es für Ihr Geschäftsszenario sinnvoll ist, können Sie Richtlinien für die Zugriffsbeschränkung für Ihr API Management-Produkt implementieren. Beispielsweise kann die `rate-limit-by-key`-Richtlinie verwendet werden, um API-Nutzungsspitzen auf Schlüsselbasis zu verhindern, indem die Aufrufrate für einen bestimmten Zeitraum beschränkt wird.

Weitere Informationen finden Sie unter [API Management-Richtlinien für die Zugriffsbeschränkung](api-management-access-restriction-policies.md).

## <a name="see-also"></a>Weitere Informationen

* [Azure Load Balancer: Problembehandlung für Fehler bei ausgehenden Verbindungen](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: Beheben zeitweiliger Fehler bei ausgehenden Verbindungen](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
