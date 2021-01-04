---
title: Netzwerksicherheit für Azure Event Grid-Ressourcen
description: In diesem Artikel wird beschrieben, wie Sie Diensttags für ausgehenden Datenverkehr, IP-Firewallregeln für eingehenden Datenverkehr und private Endpunkte für eingehenden Datenverkehr mit Azure Event Grid verwenden.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 10c9b165041f0a4a1f09511f17bef3629353c3b2
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917527"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Netzwerksicherheit für Azure Event Grid-Ressourcen
In diesem Artikel wird beschrieben, wie Sie die folgenden Sicherheitsfunktionen mit Azure Event Grid verwenden: 

- Diensttags für ausgehenden Datenverkehr
- IP-Firewallregeln für eingehenden Datenverkehr
- Private Endpunkte für eingehenden Datenverkehr


## <a name="service-tags"></a>Diensttags
Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Tag automatisch, wenn sich die Adressen ändern. Auf diese Weise wird die Komplexität häufiger Updates an Netzwerksicherheitsregeln minimiert. Weitere Informationen zu Diensttags finden Sie unter [Diensttags: Übersicht](../virtual-network/service-tags-overview.md).

Sie können Diensttags verwenden, um Netzwerkzugriffssteuerungen in [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) oder in der [Azure Firewall](../firewall/service-tags.md) zu definieren. Verwenden Sie Diensttags anstelle von spezifischen IP-Adressen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (beispielsweise **AzureEventGrid**) im entsprechenden *Quell-* oder *Zielfeld* einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern.

| Diensttag | Zweck | Eingehend oder ausgehend möglich? | Regional möglich? | Einsatz mit Azure Firewall möglich? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid: | Beide | Nein | Nein |


## <a name="ip-firewall"></a>IP-Firewall 
Azure Event Grid unterstützt IP-basierte Zugriffssteuerungen zum Veröffentlichen in Themen und Domänen. Mit IP-basierter Steuerung können Sie Herausgeber auf ein Thema oder eine Domäne beschränken, sodass Sie nur auf eine Reihe genehmigter Computer und Clouddienste festgelegt sind. Diese Funktion ergänzt die [Authentifizierungsmechanismen](security-authentication.md), die von Event Grid unterstützt werden.

Standardmäßig kann auf Themen und Domänen über das Internet zugegriffen werden, solange die Anforderung eine gültige Authentifizierung und Autorisierung aufweist. Mit der IP-Firewall können Sie den Zugriff auf eine Gruppe von IP-Adressen oder IP-Adressbereichen in [CIDR-Notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) weiter einschränken. Herausgeber, die von einer anderen IP-Adresse stammen, werden abgelehnt und erhalten eine 403-Antwort (Verboten).

Eine schrittweise Anleitung zum Konfigurieren der IP-Firewall für Themen und Domänen finden Sie unter [Konfigurieren der IP-Firewall](configure-firewall.md).

## <a name="private-endpoints"></a>Private Endpunkte
Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) verwenden, um den Eingang von Ereignissen direkt aus Ihrem virtuellen Netzwerk in Ihre Themen und Domänen sicher über einen [privaten Link](../private-link/private-link-overview.md) zu ermöglichen, ohne das öffentliche Internet zu durchlaufen. Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle für einen Azure-Dienst in Ihrem VNET. Wenn Sie einen privaten Endpunkt für Ihr Thema oder Ihre Domäne erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem VNET und der Event Grid-Ressource bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres VNET zugewiesen. Für die Verbindung zwischen dem privaten Endpunkt und dem Event Grid-Dienst wird eine sichere private Verbindung verwendet.

![Architekturdiagramm](./media/network-security/architecture-diagram.png)

Die Verwendung privater Endpunkte für Ihre Event Grid-Ressource bietet Ihnen folgende Möglichkeiten:

- Sicheren Zugriff auf Ihr Thema oder Ihre Domäne über ein VNET über das Microsoft-Backbonenetzwerk im Gegensatz zum öffentlichen Internet.
- Sicheres Verbinden aus lokalen Netzwerken, die eine Verbindung mit dem VNET über VPN oder ExpressRoute mit privatem Peering herstellen.

Wenn Sie einen privaten Endpunkt für ein Thema oder eine Domäne in Ihrem VNET erstellen, wird an den Ressourcenbesitzer eine Einwilligungsanforderung zur Genehmigung gesendet. Wenn der Benutzer, der die Erstellung des privaten Endpunkts anfordert, auch ein Besitzer der Ressource ist, wird diese Einwilligungsanforderung automatisch genehmigt. Andernfalls befindet sich die Verbindung im Status **Ausstehend**, bis sie genehmigt wurde. Anwendungen im VNET können eine nahtlose Verbindung mit dem Event Grid-Dienst über den privaten Endpunkt herstellen, und zwar mit denselben Verbindungszeichenfolgen und Autorisierungsmechanismen, die auch sonst verwendet würden. Ressourcenbesitzer können Einwilligungsanforderungen und die privaten Endpunkte über die Registerkarte **Private Endpunkte** für die Ressource im Azure-Portal verwalten.

### <a name="connect-to-private-endpoints"></a>Herstellen einer Verbindung mit privaten Endpunkten
Herausgeber in einem VNET, die den privaten Endpunkt verwenden, sollten dieselbe Verbindungszeichenfolge für das Thema oder die Domäne verwenden wie Clients, die eine Verbindung mit dem öffentlichen Endpunkt herstellen. Die DNS-Auflösung leitet Verbindungen automatisch aus dem VNET an das Thema oder die Domäne über einen privaten Link weiter. Event Grid erstellt standardmäßig eine [private DNS-Zone](../dns/private-dns-overview.md), die mit dem erforderlichen Update für die privaten Endpunkte an das VNET angefügt wird. Wenn Sie jedoch einen eigenen DNS-Server verwenden, müssen Sie möglicherweise zusätzliche Änderungen an Ihrer DNS-Konfiguration vornehmen.

### <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte
Wenn Sie einen privaten Endpunkt erstellen, wird der DNS-CNAME-Eintrag für die Ressource auf einen Alias in einer Unterdomäne mit dem Präfix `privatelink` aktualisiert. Standardmäßig wird eine private DNS-Zone erstellt, die der Unterdomäne des privaten Links entspricht. 

Wenn Sie die Endpunkt-URL des Themas oder der Domäne außerhalb des VNET mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt des Diensts aufgelöst. Die DNS-Ressourceneinträge für „topicA“ lauten nach dem Auflösen **außerhalb des VNET**, das den privaten Endpunkt hostet, wie folgt:

| Name                                          | type      | Wert                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

Sie können den Zugriff für Clients außerhalb des VNET über den öffentlichen Endpunkt mithilfe der [IP-Firewall](#ip-firewall) verweigern oder steuern. 

Bei Auflösung aus dem VNET, das den privaten Endpunkt hostet, wird die Endpunkt-URL des Themas oder der Domäne in die IP-Adresse des privaten Endpunkts aufgelöst. Die DNS-Ressourceneinträge für das Thema „topicA“ lauten nach dem Auflösen **innerhalb des VNET**, das den privaten Endpunkt hostet, wie folgt:

| Name                                          | type      | Wert                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | Ein         | 10.0.0.5

Diese Vorgehensweise ermöglicht den Zugriff auf das Thema oder die Domäne mit derselben Verbindungszeichenfolge für Clients in dem VNET, das die privaten Endpunkte hostet, und Clients außerhalb des VNET.

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, können Clients den FQDN für den Endpunkt des Themas oder der Domäne in die IP-Adresse des privaten Endpunkts auflösen. Konfigurieren Sie den DNS-Server so, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNET delegiert wird, oder konfigurieren Sie die A-Einträge für `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` mit der IP-Adresse des privaten Endpunkts.

Der empfohlene Name der DNS-Zone ist `privatelink.eventgrid.azure.net`.

### <a name="private-endpoints-and-publishing"></a>Private Endpunkte und Veröffentlichung

In der folgenden Tabelle werden die verschiedenen Zustände der Verbindung mit dem privaten Endpunkt und die Auswirkungen auf die Veröffentlichung beschrieben:

| Verbindungsstatus   |  Veröffentlichung erfolgreich (Ja/Nein) |
| ------------------ | -------------------------------|
| Genehmigt           | Ja                            |
| Rejected (Abgelehnt)           | Nein                             |
| Ausstehend            | Nein                             |
| Getrennt       | Nein                             |

Damit die Veröffentlichung erfolgreich ist, sollte der Verbindungsstatus des privaten Endpunkts **Genehmigt** sein. Wenn eine Verbindung abgelehnt wird, kann sie nicht mithilfe des Azure-Portals genehmigt werden. Die einzige Möglichkeit besteht darin, die Verbindung zu löschen und eine neue Verbindung zu erstellen.

## <a name="pricing-and-quotas"></a>Preise und Kontingente
**Private Endpunkte** sind sowohl im Basic- als auch im Premium-Tarif von Event Grid verfügbar. Mit Event Grid können bis zu 64 private Endpunktverbindungen pro Thema oder Domäne erstellt werden. 

Das Feature **IP-Firewall** ist sowohl im Basic- als auch im Premium-Tarif von Event Grid verfügbar. Es können bis zu 16 IP-Firewallregeln pro Thema oder Domäne erstellt werden.

## <a name="next-steps"></a>Nächste Schritte
Sie können die IP-Firewall für Ihre Event Grid-Ressource so konfigurieren, dass der Zugriff über das öffentliche Internet auf eine bestimmte Gruppe von IP-Adressen oder IP-Adressbereichen beschränkt wird. Schrittweise Anleitungen dazu finden Sie unter [Konfigurieren der IP-Firewall](configure-firewall.md).

Sie können private Endpunkte konfigurieren, um den Zugriff nur auf ausgewählte virtuelle Netzwerke zu beschränken. Schrittweise Anleitungen dazu finden Sie unter [Konfigurieren privater Endpunkte](configure-private-endpoints.md).

Informationen zum Beheben von Problemen mit der Netzwerkkonnektivität finden Sie unter [Beheben von Netzwerkverbindungsproblemen.](troubleshoot-network-connectivity.md)