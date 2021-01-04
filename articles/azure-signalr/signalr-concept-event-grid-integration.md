---
title: Reagieren auf Azure SignalR Service-Ereignisse
description: Verwenden Sie Azure Event Grid, um Azure SignalR Service-Ereignisse zu abonnieren. Andere Downstreamdienste können durch diese Ereignisse ausgelöst werden.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151118"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagieren auf Azure SignalR Service-Ereignisse

Azure SignalR Service-Ereignisse ermöglichen es Anwendungen, auf Clientverbindungen zu reagieren, die über moderne serverlose Architekturen verbunden oder getrennt werden. Dies geschieht ohne komplizierten Code oder teure und ineffiziente Abrufdienste.  Stattdessen werden Ereignisse über [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) an Abonnenten wie [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) oder sogar Ihren eigenen benutzerdefinierten HTTP-Listener gepusht. Bei Azure SignalR zahlen Sie nur für die tatsächliche Nutzung.

Azure SignalR Service-Ereignisse werden zuverlässig an den Event Grid-Dienst gesendet, der zuverlässige Zustellungsdienste für Ihre Anwendungen durch umfangreiche Wiederholungsrichtlinien und Zustellung unzustellbarer Nachrichten bereitstellt. Weitere Informationen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](../event-grid/delivery-and-retry.md).

![Event Grid-Modell](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Serverloser Zustand
Azure SignalR Service-Ereignisse sind nur aktiv, wenn sich Clientverbindungen in einem serverlosen Zustand befinden. Wenn ein Client keine Weiterleitung an einen Hubserver vornimmt, geht er in den serverlosen Zustand über. Der klassische Modus funktioniert nur, wenn der Hub, mit dem die Clientverbindungen eine Verbindung herstellen, keinen Hubserver besitzt. Der serverlose Modus wird als bewährte Methode empfohlen. Weitere Informationen zum Dienstmodus finden Sie unter [Auswählen des Dienstmodus](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Verfügbare Azure SignalR Service-Ereignisse
Event Grid verwendet [Ereignisabonnements](../event-grid/concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten. Azure SignalR Service-Ereignisabonnements unterstützen zwei Arten von Ereignissen:  

|Veranstaltungsname|BESCHREIBUNG|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Wird ausgelöst, wenn eine Clientverbindung verbunden wird.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Wird ausgelöst, wenn eine Clientverbindung getrennt wird.|

## <a name="event-schema"></a>Ereignisschema
Azure SignalR Service-Ereignisse enthalten alle Informationen, die Sie zur Reaktion auf Änderungen in Ihren Daten benötigen. Sie können ein Azure SignalR Service-Ereignis durch „Microsoft.SignalRService“ am Anfang der eventType-Eigenschaft identifizieren. Weitere Informationen zur Verwendung von Event Grid-Ereigniseigenschaften finden Sie unter [Event Grid-Ereignisschema](../event-grid/event-schema.md).  

Dies ist ein Beispiel für ein Ereignis des Typs „Clientverbindung verbunden“:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Weitere Informationen finden Sie unter [SignalR Service-Ereignisschema](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Event Grid, und probieren Sie Azure SignalR Service-Ereignisse aus:

> [!div class="nextstepaction"]
> [Testen einer Event Grid-Beispielintegration mit Azure SignalR Service](./signalr-howto-event-grid-integration.md)
> [Informationen zu Event Grid](../event-grid/overview.md)