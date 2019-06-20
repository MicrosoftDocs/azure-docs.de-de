---
title: Azure Event Grid-Ereignisquellen
description: Dieser Artikel beschreibt unterstützte Ereignisquellen für Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: f6c00f4ada0590df91fce0c36281d0c0cac67b77
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257949"
---
# <a name="event-sources-in-azure-event-grid"></a>Ereignisquellen in Azure Event Grid

Eine Ereignisquelle ist die Quelle, in der das Ereignis auftritt. Mehrere Azure-Dienste werden automatisch für das Senden von Ereignissen konfiguriert. Sie können auch benutzerdefinierte Anwendungen erstellen, die Ereignisse senden. Benutzerdefinierte Anwendungen müssen nicht in Azure gehostet werden, damit Event Grid für die Ereignisverteilung verwendet werden kann.

Dieser Artikel enthält Links zu Inhalten für jede Ereignisquelle.

## <a name="azure-subscriptions"></a>Azure-Abonnements

Abonnieren Sie Azure-Abonnementereignisse, um auf Änderungen an Ressourcen in einem ganzen Azure-Abonnement zu reagieren.

|Titel |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Azure Automation mit Event Grid und Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Erstellen Sie einen virtuellen Computer, der ein Ereignis gesendet. Das Ereignis löst ein Automation-Runbook, das den virtuellen Computer markiert, sowie eine Nachricht aus, die an einen Microsoft Teams-Kanal gesendet wird. |
| [Gewusst wie: Abonnieren von Ereignissen über das Portal](subscribe-through-portal.md) | Verwenden Sie das Portal, um Ereignisse für ein Azure-Abonnement zu abonnieren. |
| [Azure CLI: Abonnieren von Ereignissen für ein Azure-Abonnement](./scripts/event-grid-cli-azure-subscription.md) |Beispielskript, mit dem ein Event Grid-Abonnement für ein Azure-Abonnement erstellt wird und Ereignisse an einen WebHook gesendet werden. |
| [PowerShell: Abonnieren von Ereignissen für ein Azure-Abonnement](./scripts/event-grid-powershell-azure-subscription.md)| Beispielskript, mit dem ein Event Grid-Abonnement für ein Azure-Abonnement erstellt wird und Ereignisse an einen WebHook gesendet werden. |
| [Ereignisschema](event-schema-subscriptions.md) | Zeigt Felder in Azure-Abonnementereignissen an. |

## <a name="container-registry"></a>Containerregistrierung

Abonnieren Sie Container Registry-Ereignisse, um auf Änderungen in Images zu reagieren.

|Titel |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Senden von Container Registry-Ereignissen](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt die Verwendung von Azure CLI zum Senden von Container Registry-Ereignissen. |
| [Ereignisschema](event-schema-container-registry.md) | Zeigt Felder in Container Registry-Ereignissen. |

## <a name="custom-topics"></a>Benutzerdefinierte Themen

Abonnieren Sie benutzerdefinierte Themen, um auf Anwendungsereignisse zu reagieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure CLI](custom-event-quickstart.md) | Zeigt die Verwendung von Azure CLI zum Senden von benutzerdefinierten Ereignissen. |
| [Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure PowerShell](custom-event-quickstart-powershell.md) | Zeigt die Verwendung von Azure PowerShell zum Senden von benutzerdefinierten Ereignissen. |
| [Schnellstart: Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit dem Azure-Portal](custom-event-quickstart-portal.md) | Zeigt die Verwendung des Portals zum Senden von benutzerdefinierten Ereignissen. |
| [Schnellstart: Weiterleiten von benutzerdefinierten Ereignissen an Azure Queue Storage](custom-event-to-queue-storage.md) | Beschreibt das Senden von benutzerdefinierten Ereignisse an eine Queue Storage-Instanz. |
| [Gewusst wie: Posten eines Ereignisses in einem benutzerdefinierten Thema](post-to-custom-topic.md) | Zeigt das Posten eines Ereignisses in einem benutzerdefinierten Thema. |
| [Azure CLI: Erstellen eines benutzerdefinierten Event Grid-Themas](./scripts/event-grid-cli-create-custom-topic.md)|Beispielskript, mit dem ein benutzerdefiniertes Thema erstellt wird. Das Skript ruft den Endpunkt und einen Schlüssel ab.|
| [Azure CLI: Abonnieren von Ereignissen für ein benutzerdefiniertes Thema](./scripts/event-grid-cli-subscribe-custom-topic.md)|Beispielskript, mit dem ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an einen WebHook.|
| [PowerShell: Erstellen eines benutzerdefinierten Event Grid-Themas](./scripts/event-grid-powershell-create-custom-topic.md)|Beispielskript, mit dem ein benutzerdefiniertes Thema erstellt wird. Das Skript ruft den Endpunkt und einen Schlüssel ab.|
| [PowerShell: Abonnieren von Ereignissen für ein benutzerdefiniertes Thema](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Beispielskript, mit dem ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an einen WebHook.|
| [Resource Manager-Vorlage: Benutzerdefiniertes Thema und WebHook-Endpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Eine Resource Manager-Vorlage, mit der ein benutzerdefiniertes Thema und ein Abonnement für dieses benutzerdefinierte Thema erstellt werden. Sendet Ereignisse an einen WebHook. |
|
| [Resource Manager-Vorlage: Benutzerdefiniertes Thema und Event Hubs-Endpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Eine Resource Manager-Vorlage, mit der ein Abonnement für ein benutzerdefiniertes Thema erstellt wird. Sendet Ereignisse an Azure Event Hubs. |
| [Ereignisschema](event-schema.md) | Zeigt Felder in benutzerdefinierten Ereignissen an. |

## <a name="event-hubs"></a>Event Hubs

Abonnieren Sie Event Hubs, um auf Capture-Dateiereignisse zu reagieren. Event Hubs können entweder als Ereignisquelle oder Ereignishandler fungieren. In den folgenden Artikeln wird gezeigt, wie Event Hubs als eine Quelle verwendet werden.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Streamen von Big Data in ein Data Warehouse](event-grid-event-hubs-integration.md) | Wenn Event Hubs eine Capture-Datei erstellt, sendet Event Grid ein Ereignis an eine Funktions-App. Die App ruft die Capture-Datei ab und migriert Daten zu einem Data Warehouse. |
| [Ereignisschema](event-schema-event-hubs.md) | Zeigt Felder in Event Hubs-Ereignissen an. |

Beispiele für Event Hubs als Handler finden Sie unter [Event Hubs-Handler](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Abonnieren Sie IoT Hub-Ereignisse, um auf die Ereignisse beim Erstellen, Löschen, Verbinden und Trennen von Geräten sowie auf Telemetrieereignisse zu reagieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Eine Logik-App sendet jedes Mal eine E-Mail-Benachrichtigung, wenn Ihrer IoT Hub-Instanz ein Gerät hinzugefügt wird. |
| [Reagieren auf IoT Hub-Ereignisse mithilfe von Event Grid zum Auslösen von Aktionen](../iot-hub/iot-hub-event-grid.md) | Übersicht über die Integration von IoT Hub-Instanzen in Event Grid |
| [Ereignisschema](event-schema-iot-hub.md) | Zeigt Felder in IoT Hub-Ereignissen an. |
| [Sortieren von Ereignissen im Zusammenhang mit der Herstellung und Trennung von Geräteverbindungen](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Erfahren Sie, wie Sie Ereignisse zum Verbindungsstatus von Geräten sortieren. |

## <a name="media-services"></a>Media Services

Abonnieren Sie Media Services-Ereignisse, um auf Auftragsstatusereignisse zu reagieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Übersicht: Reagieren auf Media Services-Ereignisse](../media-services/latest/reacting-to-media-services-events.md) | Übersicht über die Integration von Media Services in Event Grid. |
| [Tutorial: Weiterleiten von Azure Media Services-Ereignissen an einen benutzerdefinierten Webendpunkt mithilfe der CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt, wie Sie Ereignisse von Media Services senden. |
| [Ereignisschema](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt Felder in Media Services-Ereignissen an. |

## <a name="resource-groups"></a>Ressourcengruppen

Abonnieren Sie Ressourcengruppenereignisse, um auf Änderungen in Ressourcen in einer ganzen Ressourcengruppe zu reagieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Überwachen von Änderungen an einem virtuellen Computer mit Azure Event Grid und Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Eine Logik-App überwacht die Änderungen an einem virtuellen Computer und sendet E-Mails zu diesen Änderungen. |
| [Azure CLI: Abonnieren von Ereignissen für eine Ressourcengruppe](./scripts/event-grid-cli-resource-group.md)| Beispielskript, mit dem Ereignisse für eine Ressourcengruppe abonniert werden. Sendet Ereignisse an einen WebHook. |
| [Azure CLI: Abonnieren von Ereignissen für eine Ressourcengruppe und Filtern nach einer Ressource](./scripts/event-grid-cli-resource-group-filter.md) | Beispielskript, mit dem Ereignisse für eine Ressourcengruppe abonniert und Ereignisse nach einer Ressource gefiltert werden. |
| [PowerShell: Abonnieren von Ereignissen für eine Ressourcengruppe](./scripts/event-grid-powershell-resource-group.md) | Beispielskript, mit dem Ereignisse für eine Ressourcengruppe abonniert werden. Sendet Ereignisse an einen WebHook. |
| [PowerShell: Abonnieren von Ereignissen für eine Ressourcengruppe und Filtern nach einer Ressource](./scripts/event-grid-powershell-resource-group-filter.md) | Beispielskript, mit dem Ereignisse für eine Ressourcengruppe abonniert und Ereignisse nach einer Ressource gefiltert werden. |
| [Resource Manager-Vorlage: Ressourcenabonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Abonniert Ereignisse für ein Azure-Abonnement oder eine Ressourcengruppe. Sendet Ereignisse an einen WebHook. |
| [Ereignisschema](event-schema-resource-groups.md) | Zeigt Felder in Ressourcengruppenereignissen an. |

## <a name="service-bus"></a>Service Bus

Abonnieren Sie Service Bus-Ereignisse, um ohne aktiven Listener auf Nachrichten zu reagieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Tutorial: Beispiele für die Integration von Azure Service Bus in Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid sendet Nachrichten von einem Service Bus-Thema an eine Funktions-App und an eine Logik-App. |
| [Übersicht: Integration von Azure Service Bus in Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Übersicht über die Integration von Service Bus in Event Grid. |
| [Ereignisschema](event-schema-service-bus.md) | Zeigt Felder in Service Bus-Ereignissen an. |

## <a name="storage"></a>Storage

Abonnieren Sie Blob Storage-Ereignisse, um auf Ereignisse der Bloberstellung und -löschung zu reagieren.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Schnellstart: Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt die Verwendung von Azure CLI zum Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Schnellstart: Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt mit PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Zeigt die Verwendung von Azure PowerShell zum Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Schnellstart: Erstellen und Weiterleiten von Blob Storage-Ereignissen mit dem Azure-Portal](blob-event-quickstart-portal.md) | Zeigt die Verwendung des Portals zum Senden von Blob Storage-Ereignissen an einen WebHook. |
| [Azure CLI: Abonnieren von Ereignissen für ein Blobspeicherkonto](./scripts/event-grid-cli-blob.md) | Beispielskript, mit dem Ereignisse für ein Blobspeicherkonto abonniert werden. Sendet das Ereignis an einen WebHook. |
| [PowerShell: Abonnieren von Ereignissen für ein Blobspeicherkonto](./scripts/event-grid-powershell-blob.md) | Beispielskript, mit dem Ereignisse für ein Blobspeicherkonto abonniert werden. Sendet das Ereignis an einen WebHook. |
| [Resource Manager-Vorlage: Erstellen eines Blobspeichers und Abonnements](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Stellt ein Azure Blob Storage-Konto bereit und abonniert Ereignisse für dieses Speicherkonto. Sendet Ereignisse an einen WebHook. |
| [Übersicht: Reaktion auf Blob Storage-Ereignisse](../storage/blobs/storage-blob-event-overview.md) | Übersicht über die Integration von Blob Storage in Event Grid. |
| [Ereignisschema](event-schema-blob-storage.md) | Zeigt Felder in Blob Storage-Ereignissen an. |

## <a name="maps"></a>Karten
Abonnieren Sie Azure Maps-Ereignisse, um auf Geofenceereignisse zu reagieren. Beispielsweise kann eine Anwendung jedes Mal, wenn ein Gerät einen Geofence betritt oder verlässt, eine E-Mail-Benachrichtigung senden.

|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Reagieren auf Azure Maps-Ereignisse mithilfe von Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Übersicht über die Integration von Azure Maps in Event Grid. |
| [Tutorial: Einrichten eines Geofence](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | In diesem Tutorial werden die grundlegenden Schritte beschrieben, die zum Einrichten eines Geofence mit Azure Maps ausgeführt werden müssen. Sie verwenden Azure Event Grid, um die Ergebnisse für den Geofence zu streamen und basierend darauf eine Benachrichtigung einzurichten. |
| [Ereignisschema](event-schema-azure-maps.md) | Zeigt Felder in Azure Maps-Ereignissen an. |


## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
