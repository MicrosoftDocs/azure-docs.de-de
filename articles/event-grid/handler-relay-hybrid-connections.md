---
title: Relay-Hybridverbindung als Ereignishandler für Azure Event Grid-Ereignisse
description: Hier wird beschrieben, wie Sie Azure Relay-Hybridverbindungen als Ereignishandler für Azure Event Grid-Ereignisse verwenden können.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 38233a2e103600f07837ce9a1ad8d63fe7e4fb99
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "91270201"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Relay-Hybridverbindung als Ereignishandler für Azure Event Grid-Ereignisse
Ein Ereignishandler ist der Ort, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses weitere Maßnahmen. Mehrere Azure-Dienste werden automatisch für die Behandlung von Ereignissen konfiguriert. **Azure Relay** ist einer dieser Dienste. 

Verwenden Sie **Azure Relay-Hybridverbindungen** zum Senden von Ereignissen an Anwendungen, die sich in einem Unternehmensnetzwerk befinden und nicht über einen öffentlich zugänglichen Endpunkt verfügen.

## <a name="tutorials"></a>Tutorials
Im folgenden Tutorial finden Sie ein Beispiel für die Verwendung einer Azure Relay-Hybridverbindung als Ereignishandler: 

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Senden von Ereignissen an eine Hybridverbindung](custom-event-to-hybrid-connection.md) | Sendet ein benutzerdefiniertes Ereignis zur Verarbeitung durch eine Listeneranwendung an eine bestehende Hybridverbindung. |

## <a name="rest-example-for-put"></a>REST-Beispiel (für PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der unterstützten Ereignishandler finden Sie im Artikel zu [Ereignishandlern](event-handlers.md). 