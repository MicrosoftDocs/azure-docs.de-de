---
title: Service Bus-Warteschlangen und -Themen als Ereignishandler für Azure Event Grid-Ereignisse
description: Hier wird beschrieben, wie Sie Service Bus-Warteschlangen und -Themen als Ereignishandler für Azure Event Grid-Ereignisse verwenden können.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 2b18009f8fb31f1a5f057c7395781f63f182847f
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "96024213"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus-Warteschlangen und -Themen als Ereignishandler für Azure Event Grid-Ereignisse
Ein Ereignishandler ist der Ort, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses weitere Maßnahmen. Mehrere Azure-Dienste werden automatisch für die Behandlung von Ereignissen konfiguriert. **Azure Service Bus** ist einer dieser Dienste. 

Sie können eine Dienstwarteschlange oder ein Dienstthema als Handler für Ereignisse von Event Grid verwenden. 

## <a name="service-bus-queues"></a>Service Bus-Warteschlangen
Sie können Ereignisse in Event Grid zur Verwendung in Puffer- oder Befehls- und Kontrollszenarien in Unternehmensanwendungen direkt an Service Bus-Warteschlangen weiterleiten.

Wählen Sie im Azure-Portal bei der Erstellung eines Ereignisabonnements **Service Bus-Warteschlange** als Endpunkttyp aus, und klicken Sie dann auf **Endpunkt auswählen**, um eine Service Bus-Warteschlange auszuwählen.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Verwenden der CLI zum Hinzufügen eines Service Bus-Warteschlangenhandlers

Für Azure CLI abonniert und verbindet das folgende Beispiel ein Event Grid-Thema mit einer Service Bus-Warteschlange:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus-Themen

Sie können Ereignisse in Event Grid direkt an Service Bus-Themen weiterleiten, um Azure-Systemereignisse mit Service Bus-Themen zu verarbeiten oder um Messagingszenarios mit Befehlen und Steuerungen zu ermöglichen.

Wählen Sie im Azure-Portal bei der Erstellung eines Ereignisabonnements **Service Bus-Thema** als Endpunkttyp aus, und klicken Sie dann auf **Endpunkt auswählen**, um ein Service Bus-Thema auszuwählen.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Verwenden der CLI zum Hinzufügen eines Service Bus-Themenhandlers

Für Azure CLI abonniert und verbindet das folgende Beispiel ein Event Grid-Thema mit einer Service Bus-Warteschlange:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Beim Senden eines Ereignisses an eine Service Bus-Warteschlange oder ein Service Bus-Thema als im Broker gespeicherte Nachricht ist `messageid` dieser Nachricht eine interne System-ID.

Die interne System-ID für die Nachricht wird bei der erneuten Übermittlung des Ereignisses beibehalten. Sie können also doppelte Übermittlungen vermeiden, indem Sie die **Duplikaterkennung** für die Service Bus-Entität aktivieren. Es wird empfohlen, die Dauer der Duplikaterkennung für die Service Bus-Entität entweder auf die Gültigkeitsdauer (Time-To-Live, TTL) des Ereignisses oder auf den maximalen Wiederholungszeitraum festzulegen, je nachdem, welcher Zeitraum länger ist.

## <a name="rest-examples-for-put"></a>REST-Beispiele (für PUT)

### <a name="service-bus-queue"></a>Service Bus-Warteschlange

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus-Warteschlange: Bereitstellung mit verwalteter Identität

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus-Topic

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus-Thema: Bereitstellung mit verwalteter Identität

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der unterstützten Ereignishandler finden Sie im Artikel zu [Ereignishandlern](event-handlers.md). 
