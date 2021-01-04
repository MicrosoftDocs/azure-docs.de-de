---
title: 'Azure Event Grid: Partnerereignisse'
description: Mit Azure Event Grid können Sie Ereignisse von SaaS- und PaaS-Drittanbieterpartnern direkt an Azure-Dienste senden.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506145"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Partnerereignisse in Azure Event Grid (Vorschau)
Mit dem Feature **Partnerereignisse** kann ein SaaS-Drittanbieter Ereignisse über seine Dienste veröffentlichen, damit Kunden diese Ereignisse abonnieren können. Dieses Feature bietet Erstanbieterfunktionen für Ereignisquellen von Drittanbietern, indem ein [Thementyp](concepts.md#topics) zur Verfügung gestellt wird: ein **Partnerthema**. Abonnenten erstellen Abonnements dieses Themas, um Ereignisse zu nutzen. Außerdem bietet dieses Feature ein klares Modell für Veröffentlichungen/Abonnements, indem es die Belange und den Besitz von Ressourcen trennt, die von Ereignisherausgebern und -abonnenten verwendet werden.

> [!NOTE]
> Wenn Sie noch nicht mit Event Grid vertraut sind, sehen Sie sich die Themen [Übersicht](overview.md), [Konzepte](concepts.md) und [Ereignishandler](event-handlers.md) an.

## <a name="what-is-partner-events-to-a-publisher"></a>Was ermöglicht das Feature „Partnerereignisse“ einem Herausgeber?
Einem Ereignisherausgeber ermöglicht das Feature „Partnerereignisse“ das Ausführen der folgenden Aufgaben:

- Einbinden seiner Ereignisquellen in Event Grid
- Erstellen eines Namespaces (Endpunkts), in dem Ereignisse veröffentlicht werden können
- Erstellen von Partnerthemen in Azure, die Abonnenten zum Nutzen von Ereignissen besitzen und verwenden

## <a name="what-is-partner-events-to-a-subscriber"></a>Was ermöglicht das Feature „Partnerereignisse“ einem Abonnenten?
Einem Abonnenten ermöglicht das Feature „Partnerereignisse“, Partnerthemen in Azure zu erstellen, um Ereignisse aus Ereignisquellen von Drittanbietern zu nutzen. Die Nutzung von Ereignissen wird durch die Erstellung von Ereignisabonnements ermöglicht, die Ereignisse (per Push) an den Ereignishandler eines Abonnenten senden.

## <a name="why-should-i-use-partner-events"></a>Was spricht für das Feature „Partnerereignisse“?
Das Feature „Partnerereignisse“ kommt für Sie in Frage, wenn Sie eine oder mehrere der folgenden Anforderungen haben.

### <a name="for-publishers"></a>Für Herausgeber

- Sie wünschen einen Mechanismus, um Ihre Ereignisse in Azure verfügbar zu machen. Ihre Benutzer können diese Ereignisse filtern und weiterleiten, indem sie Partnerthemen und Ereignisabonnements verwenden, die sie besitzen und verwalten. Sie können auch andere Integrationsansätze verwenden, z. B. [Themen](custom-topics.md) und [Domänen](event-domains.md). Allerdings würden diese keine klare Trennung von Besitz, Verwaltung und Abrechnung von Ressourcen (Partnerthemen) zwischen Herausgebern und Abonnenten ermöglichen. Außerdem bietet dieser Ansatz eine intuitivere Benutzererfahrung, die das Entdecken und Verwenden von Partnerthemen erleichtert.
- Es empfiehlt sich, Ereignisse an einem einzigen Endpunkt, und zwar dem des Namespaces, zu veröffentlichen. Außerdem wünschen Sie sich die Möglichkeit, diese Ereignisse so zu filtern, dass nur eine Teilmenge von ihnen verfügbar ist. 
- Sie möchten einen Einblick in Metriken haben, die sich auf veröffentlichte Ereignisse beziehen.
- Sie möchten das Schema [Cloud Events 1.0](https://cloudevents.io/) für Ihre Ereignisse verwenden.

### <a name="for-subscribers"></a>Für Abonnenten

- Sie möchten Ereignisse von [Drittanbieterherausgebern](#available-third-party-event-publishers) abonnieren und die Ereignisse mit Ereignishandlern verarbeiten, die sich in Azure oder anderswo befinden.
- Sie möchten die zahlreichen Routingfeatures und [Ziele/Ereignishandler](overview.md#event-handlers) nutzen, um Ereignisse aus Ereignisquellen von Drittanbietern zu verarbeiten. 
- Sie möchten lose gekoppelte Architekturen implementieren, bei denen der Abonnent/Ereignishandler nicht weiß, ob der Nachrichtenbroker vorhanden ist. 
- Sie benötigen einen zuverlässigen Push-Übermittlungsmechanismus mit Unterstützung für Sendewiederholungen und einer Semantik des Typs „Mindestens einmal“.
- Sie möchten das Schema [Cloud Events 1.0](https://cloudevents.io/) für Ihre Ereignisse verwenden. 


## <a name="available-third-party-event-publishers"></a>Verfügbare Ereignisherausgeber von Drittanbietern
Ein Ereignisherausgeber von Drittanbietern muss einen [Onboardingprozess durchlaufen](partner-onboarding-overview.md), ehe ein Abonnent mit der Nutzung seiner Ereignisse beginnen kann. 

Wenn Sie Abonnent sind und möchten, dass ein Drittanbieter seine Ereignisse über Event Grid verfügbar macht, 

### <a name="auth0"></a>Auth0
**Auth0** ist der erste verfügbare Partnerherausgeber. Sie können ein [Auth0-Partnerthema](auth0-overview.md) erstellen, um Ihre Auth0- und Azure-Konten zu verbinden. Diese Integration ermöglicht Ihnen, in Echtzeit auf Auth0-Ereignisse zu reagieren, diese zu protokollieren und zu überwachen. Weitere Informationen finden Sie unter [Integrieren von Azure Event Grid und Auth0](auth0-how-to.md).

Wenn Sie möchten, dass ein Drittanbieterdienst seine Ereignisse über Event Grid verfügbar macht, übermitteln Sie den Vorschlag im [User Voice-Portal](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Von Ereignisherausgebern verwaltete Ressourcen
Die folgenden Ressourcen werden von Ereignisherausgebern erstellt und verwaltet:

### <a name="partner-registration"></a>Partnerregistrierung
Eine Registrierung enthält allgemeine Informationen zu einem Herausgeber. Sie definiert eine Art von Partnerthema, das im Azure-Portal als Option angezeigt wird, wenn Benutzer versuchen, ein Partnerthema anzulegen. Ein Herausgeber kann mehr als einen oder mehrere Partnerthementypen verfügbar machen, um den Bedürfnissen seiner Abonnenten gerecht zu werden. Das heißt, ein Herausgeber kann separate Registrierungen (Typen von Partnerthemen) für Ereignisse aus verschiedenen Diensten erstellen. Beispielsweise kann der Herausgeber für den Dienst „Personalwesen“ ein Partnerthema für Ereignisse wie Eintritt, Beförderung und Ausscheiden eines Mitarbeiters aus dem Unternehmen veröffentlichen. 

Berücksichtigen Sie dabei Folgendes:

- Nur von Azure genehmigte Partnerregistrierungen sind sichtbar. 
- Registrierungen sind global. Das heißt, dass sie keiner bestimmten Azure-Region zugeordnet sind.
- Eine Registrierung ist eine optionale Ressource. Es wird jedoch empfohlen, dass Sie (als Herausgeber) eine Registrierung erstellen. Sie ermöglicht Benutzern, Ihre Themen im [Azure-Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic) auf der Seite **Partnerthema erstellen** zu entdecken. Anschließend kann der Benutzer beim Erstellen von Ereignisabonnements Ereignistypen auswählen (z. B. Eintritt des Mitarbeiters, Ausscheiden des Mitarbeiters usw.).

### <a name="namespace"></a>Namespace
Wie [benutzerdefinierte Themen](custom-topics.md) und [Domänen](event-domains.md) ist ein Partnernamespace ein regionaler Endpunkt für die Veröffentlichung von Ereignissen. Namespaces dienen Herausgebern zum Erstellen und Verwalten von Kanälen für Ereignisse. Ein Namespace fungiert auch als Containerressource für Ereigniskanäle.

### <a name="event-channels"></a>Ereigniskanäle
Ein Ereigniskanal ist eine gespiegelte Ressource zu einem Partnerthema. Wenn ein Herausgeber einen Ereigniskanal im Azure-Abonnement des Herausgebers anlegt, erstellt er auch ein Partnerthema im Azure-Abonnement eines Abonnenten. Die Vorgänge, die für einen Ereigniskanal (mit Ausnahme von GET) erfolgen, werden auf das entsprechende Partnerthema des Abonnenten angewendet, einschließlich Löschung. Allerdings sind nur Partnerthemen die Art von Ressourcen, für die Abonnements und die Ereigniszustellung konfiguriert werden können.

## <a name="resources-managed-by-subscribers"></a>Von Abonnenten verwaltete Ressourcen 
Abonnenten können Partnerthemen verwenden, die von einem Herausgeber definiert wurden. Dies ist die einzige Art von Ressource, die sie sehen und verwalten. Nachdem ein Partnerthema erstellt wurde, kann ein Abonnement Ereignisabonnements erstellen, die Filterregeln zu [Zielen/Ereignishandlern](overview.md#event-handlers) definieren. Für Abonnenten bieten ein Partnerthema und die damit verbundenen Ereignisabonnements die gleichen umfangreichen Möglichkeiten wie [benutzerdefinierte Themen](custom-topics.md) und die damit verbundenen Abonnements mit einem nennenswerten Unterschied: Partnerthemen unterstützen nur das Schema [Cloud Events 1.0](cloudevents-schema.md), das im Vergleich zu anderen unterstützten Schemas einen größeren Funktionsumfang bietet.

Die folgende Abbildung veranschaulicht den Ablauf der Vorgänge auf der Steuerungsebene.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="Partnerereignisse: Ablauf der Steuerungsebene":::

1. Der Herausgeber erstellt eine **Partnerregistrierung**. Partnerregistrierungen sind global. Das heißt, dass sie keiner bestimmten Azure-Region zugeordnet sind. Dieser Schritt ist optional.
1. Der Herausgeber erstellt einen **Partnernamespace** in einer bestimmten Region.
1. Wenn Abonnent 1 versucht, ein Partnerthema zu erstellen, wird zuerst ein **Ereigniskanal** (Event Channel 1) im Azure-Abonnement des Herausgebers erstellt.
1. Dann wird ein **Partnerthema** (Partner Topic 1) im Azure-Abonnement des Abonnenten erstellt. Der Abonnent muss das Partnerthema aktivieren. 
1. Abonnent 1 erstellt ein **Azure Logic Apps-Abonnement** für Partner Topic 1.
1. Abonnent 1 erstellt ein **Azure Blob Storage-Abonnement** für Partner Topic 1. 
1. Wenn Abonnent 2 versucht, ein Partnerthema zu erstellen, wird zuerst ein weiterer **Ereigniskanal** (Event Channel 2) im Azure-Abonnement des Herausgebers erstellt. 
1. Dann wird das **Partnerthema** (Partner Topic 2) im Azure-Abonnement des zweiten Abonnenten erstellt. Der Abonnent muss das Partnerthema aktivieren. 
1. Abonnent 2 erstellt ein **Azure Functions-Abonnement** für Partner Topic 2. 

## <a name="pricing"></a>Preise
Partnerthemen werden nach Anzahl der Vorgänge in Rechnung gestellt, die bei Verwendung von Event Grid erfolgen. Weitere Informationen zu allen Arten von Vorgängen, die als Grundlage für die Abrechnung herangezogen werden, sowie detaillierte Preisinformationen finden Sie unter [Event Grid: Preise](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="limits"></a>Einschränkungen
Ausführliche Informationen zu den für Partnerthemen geltenden Grenzwerten finden Sie unter [Grenzwerte für Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).


## <a name="next-steps"></a>Nächste Schritte

- [Auth0-Partnerthema](auth0-overview.md)
- [Integrieren von Azure Event Grid mit Auth0](auth0-how-to.md)
- [Registrieren als Event Grid-Partner](partner-onboarding-overview.md)