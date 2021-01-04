---
title: Speicherwarteschlange als Ereignishandler für Azure Event Grid-Ereignisse
description: Hier wird beschrieben, wie Sie Azure-Speicherwarteschlangen als Ereignishandler für Azure Event Grid-Ereignisse verwenden können.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 502b44f276253be69362424c9de0fd516d20ad9a
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "91270184"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Speicherwarteschlange als Ereignishandler für Azure Event Grid-Ereignisse
Ein Ereignishandler ist der Ort, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses weitere Maßnahmen. Mehrere Azure-Dienste werden automatisch für die Behandlung von Ereignissen konfiguriert. **Azure Queue Storage** ist einer dieser Dienste. 

Verwenden Sie **Queue Storage**, um Ereignisse zu empfangen, die gepullt werden müssen. Bei einem Prozess mit langer Ausführungszeit und zu langer Antwortdauer können Sie Queue Storage verwenden. Wenn die Ereignisse an Queue Storage gesendet werden, kann die App Ereignisse nach einem eigenen Zeitplan pullen und verarbeiten.

## <a name="tutorials"></a>Tutorials
Im folgenden Tutorial finden Sie ein Beispiel für die Verwendung von Queue Storage als Ereignishandler. 

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Weiterleiten benutzerdefinierter Ereignisse an Azure Queue Storage mit Azure CLI und Event Grid](custom-event-to-queue-storage.md) | Beschreibt das Senden von benutzerdefinierten Ereignisse an eine Queue Storage-Instanz. |

## <a name="rest-examples-for-put"></a>REST-Beispiele (für PUT)

### <a name="storage-queue-as-the-event-handler"></a>Speicherwarteschlange als Ereignishandler

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                "queueName": "<QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler---delivery-with-managed-identity"></a>Speicherwarteschlange als Ereignishandler: Übermittlung mit verwalteter Identität

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
                "endpointType": "StorageQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                    "queueName": "<QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination"></a>Speicherwarteschlange als Ziel für unzustellbare Nachrichten

```json
{
    "name": "",
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterDestination": 
        {
            "endpointType": "StorageBlob",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                "blobContainerName": "test"
            }
        }
    }
}
```

### <a name="storage-queue-as-a-deadletter-destination---managed-identity"></a>Speicherwarteschlange als Ziel für unzustellbare Nachrichten: verwaltete Identität

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "deadLetterDestination": 
            {
                "endpointType": "StorageBlob",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                    "blobContainerName": "test"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der unterstützten Ereignishandler finden Sie im Artikel zu [Ereignishandlern](event-handlers.md). 
