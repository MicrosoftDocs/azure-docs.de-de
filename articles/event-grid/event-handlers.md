---
title: Azure Event Grid-Ereignishandler
description: Informationen zu unterstützten Ereignishandlern für Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 915d1284d66438219fc9aba893512e5f6a5b02b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305043"
---
# <a name="event-handlers-in-azure-event-grid"></a>Ereignishandler in Azure Event Grid

Ein Ereignishandler ist der Ort, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses weitere Maßnahmen. Mehrere Azure-Dienste werden automatisch für die Behandlung von Ereignissen konfiguriert. Sie können aber auch einen beliebigen WebHook für die Behandlung von Ereignissen verwenden. Der WebHook muss zum Behandeln von Ereignissen nicht in Azure gehostet werden. Event Grid unterstützt nur HTTPS-WebHook-Endpunkte.

Dieser Artikel enthält Links zu Inhalten für die einzelnen Ereignishandler.

## <a name="azure-automation"></a>Azure-Automatisierung

Verwenden Sie Azure Automation für die Verarbeitung von Ereignissen mit automatisierten Runbooks.

|Titel  |BESCHREIBUNG  |
|---------|---------|
|[Tutorial: Azure Automation mit Event Grid und Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Erstellen Sie einen virtuellen Computer, der ein Ereignis gesendet. Das Ereignis löst ein Automation-Runbook, das den virtuellen Computer markiert, sowie eine Nachricht aus, die an einen Microsoft Teams-Kanal gesendet wird. |

## <a name="azure-functions"></a>Azure-Funktionen

Verwenden Sie Azure Functions für eine serverlose Reaktion auf Ereignisse.

Wenn Sie Azure Functions als Handler einsetzen, verwenden Sie den Event Grid-Trigger anstelle der generischen HTTP-Trigger. Event Grid überprüft Event Grid-Funktionstrigger automatisch. Bei generischen HTTP-Triggern müssen Sie die [Überprüfungsantwort](security-authentication.md#webhook-event-delivery) implementieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Event Grid-Trigger für Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Übersicht über die Verwendung des Event Grid-Triggers in Functions. |
| [Tutorial: Automatisieren der Größenänderung von hochgeladenen Images per Event Grid](resize-images-on-storage-blob-upload-event.md) | Benutzer laden Bilder über eine Web-App in ein Speicherkonto hoch. Wenn ein Speicherblob erstellt wird, sendet Event Grid ein Ereignis an die Funktions-App, die daraufhin die Größe des hochgeladenen Bilds anpasst. |
| [Tutorial: Streamen von Big Data in ein Data Warehouse](event-grid-event-hubs-integration.md) | Wenn Event Hubs eine Capture-Datei erstellt, sendet Event Grid ein Ereignis an eine Funktions-App. Die App ruft die Capture-Datei ab und migriert Daten zu einem Data Warehouse. |
| [Tutorial: Beispiele für die Integration von Azure Service Bus in Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid sendet Nachrichten von einem Service Bus-Thema an eine Funktions-App und an eine Logik-App. |

## <a name="event-hubs"></a>Event Hubs

Verwenden Sie Event Hubs, wenn Ihre Lösung Ereignisse schneller abruft, als die Ereignisse verarbeitet werden können. Die Anwendung verarbeitet die Ereignisse von Event Hubs nach einem eigenen Zeitplan. Sie können die Ereignisverarbeitung in Anpassung an die eingehenden Ereignisse skalieren.

Event Hubs können entweder als Ereignisquelle oder Ereignishandler fungieren. In den folgenden Artikeln wird gezeigt, wie Event Hubs als Handler verwendet werden.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Weiterleiten benutzerdefinierter Ereignisse an Azure Event Hubs mit Azure CLI und Event Grid](custom-event-to-eventhub.md) | Sendet ein benutzerdefiniertes Ereignis an einen Event Hub, damit es durch eine Anwendung verarbeitet wird. |
| [Resource Manager-Vorlage: Benutzerdefiniertes Thema und Event Hubs-Endpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Eine Resource Manager-Vorlage, mit der ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an Azure Event Hubs. |

Beispiele für Event Hubs als Quelle finden Sie unter [Event Hubs-Quelle](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hybridverbindungen

Verwenden Sie Azure Relay Hybrid Connections zum Senden von Ereignissen an Anwendungen, die sich in einem Unternehmensnetzwerk befinden und nicht über einen öffentlich zugänglichen Endpunkt verfügen.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Senden von Ereignissen an eine Hybridverbindung](custom-event-to-hybrid-connection.md) | Sendet ein benutzerdefiniertes Ereignis zur Verarbeitung durch eine Listeneranwendung an eine bestehende Hybridverbindung. |

## <a name="logic-apps"></a>Logic Apps

Verwenden Sie Logic Apps, um Geschäftsprozesse für die Reaktion auf Ereignisse zu automatisieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Überwachen von Änderungen an einem virtuellen Computer mit Azure Event Grid und Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Eine Logik-App überwacht die Änderungen an einem virtuellen Computer und sendet E-Mails zu diesen Änderungen. |
| [Tutorial: Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Eine Logik-App sendet jedes Mal eine E-Mail-Benachrichtigung, wenn Ihrer IoT Hub-Instanz ein Gerät hinzugefügt wird. |
| [Tutorial: Beispiele für die Integration von Azure Service Bus in Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid sendet Nachrichten von einem Service Bus-Thema an eine Funktions-App und an eine Logik-App. |

## <a name="service-bus-queue-preview"></a>Service Bus-Warteschlange (Vorschauversion)

Verwenden Sie Service Bus als Ereignishandler, um die Ereignisse in Event Grid direkt an Service Bus-Warteschlangen für die Verwendung in Puffer- oder Befehls- und Kontrollszenarien in Unternehmensanwendungen weiterzuleiten. Die Vorschauversion funktioniert nicht mit Service Bus-Themen und -Sitzungen, aber mit allen Tarifen von Service Bus-Warteschlangen.

Beachten Sie, dass Sie, solange sich Service Bus als Handler in der öffentlichen Vorschauphase befindet, die CLI oder PowerShell-Erweiterung installieren müssen, wenn Sie diese zum Erstellen von Ereignisabonnements verwenden.

### <a name="using-cli"></a>Verwenden von CLI

Für Azure CLI abonniert und verbindet das folgende Beispiel ein Event Grid-Thema mit einer Service Bus-Warteschlange:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Queue Storage

Verwenden Sie Queue Storage, um Ereignisse zu empfangen, die gepullt werden müssen. Bei einem Prozess mit langer Ausführungszeit und zu langer Antwortdauer können Sie Queue Storage verwenden. Wenn die Ereignisse an Queue Storage gesendet werden, kann die App Ereignisse nach einem eigenen Zeitplan pullen und verarbeiten.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Weiterleiten benutzerdefinierter Ereignisse an Azure Queue Storage mit Azure CLI und Event Grid](custom-event-to-queue-storage.md) | Beschreibt das Senden von benutzerdefinierten Ereignisse an eine Queue Storage-Instanz. |

## <a name="webhooks"></a>WebHooks

Verwenden Sie Webhooks für anpassbare Endpunkte, die auf Ereignisse reagieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) und dem [Portal](custom-event-quickstart-portal.md) | Zeigt das Senden von benutzerdefinierten Ereignissen an einen WebHook. |
| Schnellstart: Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) und dem [Portal](blob-event-quickstart-portal.md) | Zeigt das Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Schnellstart: Senden von Container Registry-Ereignissen](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt die Verwendung von Azure CLI zum Senden von Container Registry-Ereignissen. |
| [Übersicht: Empfangen von Ereignissen an einem HTTP-Endpunkt](receive-events.md) | Beschreibt, wie Sie einen HTTP-Endpunkt überprüfen, um Ereignisse von einem Ereignisabonnement zu empfangen, und Ereignisse empfangen und deserialisieren. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
