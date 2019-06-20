---
title: Grundlegendes zu benutzerdefinierten Azure IoT Hub-Endpunkten | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Verwenden von Routingabfragen zum Weiterleiten von D2C-Nachrichten an benutzerdefinierte Endpunkte.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61244343"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Verwenden von Nachrichtenrouten und benutzerdefinierten Endpunkten für D2C-Nachrichten

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Mit der IoT Hub-[Nachrichtenweiterleitung](iot-hub-devguide-routing-query-syntax.md) können Benutzer D2C-Nachrichten an dienstseitige Endpunkte weiterleiten. Die Weiterleitung verfügt auch über eine Abfragefunktion zum Filtern der Daten, bevor diese an die Endpunkte weitergeleitet werden. Jede Routingabfrage, die Sie konfigurieren, hat die folgenden Eigenschaften:

| Eigenschaft      | BESCHREIBUNG |
| ------------- | ----------- |
| **Name**      | Der eindeutige Name, mit dem die Abfrage identifiziert wird. |
| **Quelle**    | Der Ursprung des zu verarbeitenden Datenstroms. Beispiel: Gerätetelemetrie. |
| **Condition** | Der Abfrageausdruck für die Routingabfrage, die für die Eigenschaften der Nachrichtenanwendung, Systemeigenschaften, den Nachrichtentext, Gerätezwillingstags und Gerätezwillingseigenschaften ausgeführt wird, um zu ermitteln, ob es sich um eine Übereinstimmung für den Endpunkt handelt. Weitere Informationen zur Erstellung einer Abfrage finden Sie im Artikel zur [Abfragesyntax für die Nachrichtenweiterleitung](iot-hub-devguide-routing-query-syntax.md). |
| **Endpunkt**  | Der Name des Endpunkts, an den vom IoT Hub diejenigen Nachrichten gesendet werden, für die sich eine Übereinstimmung mit der Abfrage ergeben hat. Es wird empfohlen, einen Endpunkt in derselben Region zu wählen, in der sich auch Ihr IoT-Hub befindet. |

Es kann vorkommen, dass sich für eine einzelne Nachricht Übereinstimmungen mit den Bedingungen mehrerer Routingabfragen ergeben. In diesem Fall sendet der IoT-Hub die Nachricht jeweils an alle Endpunkte, die den entsprechenden Abfragen zugeordnet sind. Der IoT-Hub führt bei der Nachrichtenzustellung eine automatische Deduplizierung durch. Wenn also eine Nachricht mit mehreren Abfragen mit demselben Ziel übereinstimmt, wird sie nur einmal in das Ziel geschrieben.

## <a name="endpoints-and-routing"></a>Endpunkte und Routing

Eine IoT Hub-Instanz verfügt standardmäßig über einen [integrierten Endpunkt](iot-hub-devguide-messages-read-builtin.md). Sie können benutzerdefinierte Endpunkte für das Routing von Nachrichten erstellen, indem Sie andere Dienste Ihres Abonnements mit dem Hub verknüpfen. IoT Hub unterstützt derzeit Azure Storage Container, Event Hubs, Service Bus-Warteschlangen und Service Bus-Themen als benutzerdefinierte Endpunkte.

Bei Verwendung von Routing und benutzerdefinierten Endpunkten werden Nachrichten nur an den integrierten Endpunkt übermittelt, wenn sich keine Übereinstimmung mit einer Abfrage ergibt. Fügen Sie für die Übermittlung von Nachrichten an den integrierten Endpunkt und einen benutzerdefinierten Endpunkt eine Route hinzu, die Nachrichten an den integrierten Endpunkt **events** sendet.

> [!NOTE]
> * IoT Hub unterstützt nur das Schreiben von Daten in Azure Storage-Container als BLOBs.
> * Service Bus-Warteschlangen und -Themen, bei denen **Sitzungen** oder **Duplikaterkennung** aktiviert wurden, werden nicht als benutzerdefinierte Endpunkte unterstützt.

Weitere Informationen zur Erstellung von benutzerdefinierten Endpunkten auf dem IoT Hub finden Sie unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md).

Weitere Informationen zum Lesen von benutzerdefinierten Endpunkten finden Sie unter:

* Lesen aus [Azure Storage-Containern](../storage/blobs/storage-blobs-introduction.md).

* Lesen aus [Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Lesen aus [Service Bus-Warteschlangen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Lesen aus [Service Bus-Themen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu IoT Hub-Endpunkten finden Sie unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md).

* Weitere Informationen zur Abfragesprache, die Sie zum Definieren von Routingabfragen verwenden, finden Sie im Artikel zur [Abfragesyntax für die Nachrichtenweiterleitung](iot-hub-devguide-routing-query-syntax.md).

* Im Tutorial [Verarbeiten von IoT Hub-D2C-Nachrichten mithilfe von Routen](tutorial-routing.md) erfahren Sie, wie Sie Routingabfragen und benutzerdefinierte Endpunkte verwenden.