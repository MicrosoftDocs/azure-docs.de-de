---
author: baanders
description: Include-Datei für die Ressourcen, die zum Erstellen von Azure Digital Twins-Endpunkten benötigt werden
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "99054505"
---
### <a name="prerequisite-create-endpoint-resources"></a>Voraussetzung: Erstellen von Endpunktressourcen

Das für den Endpunkt verwendete Event Grid-Thema, der Event Hub oder das Service Bus-Thema muss bereits vorhanden sein, um einen Endpunkt mit Azure Digital Twins zu verknüpfen.

Verwenden Sie die folgenden Tabelle, um herauszufinden, welche Ressourcen eingerichtet werden sollten, bevor Sie den Endpunkt erstellen.

| Endpunkttyp | Erforderliche Ressourcen (mit Erstellungsanweisungen verknüpft) |
| --- | --- |
| Event Grid-Endpunkt | [Event Grid-Thema](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs-Endpunkt | [Event&nbsp;Hubs-Namespace](../articles/event-hubs/event-hubs-create.md)<br/><br/>[Event Hub](../articles/event-hubs/event-hubs-create.md)<br/><br/>(Optional) [Autorisierungsregel](../articles/event-hubs/authorize-access-shared-access-signature.md) für schlüsselbasierte Authentifizierung | 
| Service Bus-Endpunkt | [Service Bus-Namespace](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus-Topic](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (Optional) [Autorisierungsregel](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) für schlüsselbasierte Authentifizierung|
