---
title: Azure IoT Hub und Event Grid | Microsoft-Dokumentation
description: Lösen Sie mit Azure Event Grid Prozesse auf der Basis von Aktionen aus, die in IoT Hub auftreten.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 73a9aebfd0c5338f63927860ce3f6c57b20428a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754778"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagieren auf IoT Hub-Ereignisse mithilfe von Event Grid zum Auslösen von Aktionen

Azure IoT Hub ist in Azure Event Grid integriert, sodass Sie Ereignisbenachrichtigungen an andere Dienste senden und nachfolgende Prozesse auslösen können. Konfigurieren Sie Ihre Geschäftsanwendungen zum Lauschen auf IoT Hub-Ereignisse, sodass Sie zuverlässig, skalierbar und sicher auf kritische Ereignisse reagieren können. Erstellen Sie beispielsweise eine Anwendung, die bei jeder Registrierung eines neuen IoT-Geräts bei Ihrem IoT-Hub eine Datenbank aktualisiert, einen Akkordzettel erstellt und eine E-Mail-Benachrichtigung generiert.

[Azure Event Grid](../event-grid/overview.md) ist ein vollständig verwalteter Ereignisroutingdienst, der ein Veröffentlichen-Abonnieren-Modell verwendet. Event Grid verfügt über integrierte Unterstützung für Azure-Dienste wie [Azure Functions](../azure-functions/functions-overview.md) und [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) und kann mithilfe von Webhooks Ereigniswarnungen an Nicht-Azure-Dienste übermitteln. Eine vollständige Liste der Ereignishandler, die Event Grid unterstützt, finden Sie unter [Einführung in Azure Event Grid](../event-grid/overview.md).

![Azure Event Grid-Architektur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Die Event Grid-Integration ist für IoT-Hubs verfügbar, die sich in den Regionen befinden, in denen Event Grid unterstützt wird. Alle Geräteereignisse mit Ausnahme von Gerätetelemetrieereignissen sind allgemein verfügbar. Gerätetelemetrieereignisse stehen in der öffentlichen Vorschauversion zur Verfügung, und zwar in allen Regionen mit Ausnahme von „USA, Osten“, „USA, Westen“, „Europa, Westen“, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china) und [Azure Deutschland](https://azure.microsoft.com/global-infrastructure/germany/). Die aktuelle Liste der Regionen finden Sie unter [Einführung in Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Ereignistypen

IoT Hub veröffentlicht die folgenden Ereignistypen:

| Ereignistypen | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Wird ausgelöst, wenn ein Gerät bei einem IoT Hub registriert wird. |
| Microsoft.Devices.DeviceDeleted | Wird ausgelöst, wenn ein Gerät aus einem IoT Hub gelöscht wird. |
| Microsoft.Devices.DeviceConnected | Wird ausgelöst, wenn ein Gerät mit einem IoT Hub verbunden wird. |
| Microsoft.Devices.DeviceDisconnected | Wird ausgelöst, wenn ein Gerät von einem IoT Hub getrennt wird. |
| Microsoft.Devices.DeviceTelemetry | Wird ausgelöst, wenn eine Gerätetelemetrienachricht an eine IoT Hub-Instanz gesendet wird. |

Konfigurieren Sie entweder mit dem Azure-Portal oder der Azure CLI, welche Ereignisse von jedem IoT Hub veröffentlicht werden sollen. Ein Beispiel finden Sie in dem Tutorial [Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Logik-Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Ereignisschema

IoT Hub-Ereignisse enthalten alle Informationen, die Sie für die Reaktion auf Änderungen in Ihrem Gerätelebenszyklus benötigen. Um ein IoT Hub-Ereignis zu identifizieren, überprüfen Sie, ob die Eigenschaft eventType mit **Microsoft.Devices** beginnt. Weitere Informationen zur Verwendung von Event Grid-Ereigniseigenschaften finden Sie unter [Azure Event Grid-Ereignisschema](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schema für „Gerät verbunden“

Das folgende Beispiel zeigt das Schema eines Ereignisses „Gerät verbunden“:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Schema der Gerätetelemetrie

Gerätetelemetrienachrichten müssen in einem gültigen JSON-Format vorliegen, und in den [Systemeigenschaften](iot-hub-devguide-routing-query-syntax.md#system-properties) der Nachricht müssen contentType auf JSON und contentEncoding auf UTF-8 festgelegt sein. Wenn dies nicht festgelegt ist, schreibt IoT Hub die Nachrichten in Base64-codiertem Format.

Sie können Telemetriegeräteereignisse anreichern, bevor sie in Event Grid veröffentlicht werden, indem Sie den Endpunkt als Event Grid auswählen. Weitere Informationen finden Sie unter [Nachrichtenanreicherungen: Übersicht](iot-hub-message-enrichments-overview.md).

Das folgende Beispiel zeigt das Schema eines Gerätetelemetrieereignisses:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Vom Gerät erstelltes Schema

Das folgende Beispiel zeigt das Schema eines von einem Gerät erstellten Ereignisses:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Eine ausführliche Beschreibung der einzelnen Eigenschaften finden Sie unter [Azure Event Grid-Ereignisschema für IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtern von Ereignissen

IoT Hub-Ereignisabonnements können Ereignisse basierend auf Ereignistyp, Dateninhalt und Betreff (der Gerätename) filtern.

Event Grid ermöglicht das [Filtern](../event-grid/event-filtering.md) nach Ereignistypen, Betreff und Dateninhalt. Beim Erstellen des Event Grid-Abonnements können Sie wählen, dass Sie ausgewählte IoT-Ereignisse abonnieren möchten. Betrefffilter in Event Grid funktionieren anhand von Übereinstimmungen bei **Beginnt mit** (Präfix) und **Endet auf** (Suffix). Der Filter verwendet einen `AND`-Operator, sodass Ereignisse mit einem Betreff, der sowohl mit dem Präfix als auch mit dem Suffix übereinstimmt, an den Abonnenten übermittelt werden.

Der Betreff von IoT-Ereignissen verwendet das Format:

```json
devices/{deviceId}
```

Event Grid ermöglicht auch das Filtern nach Attributen jedes Ereignisses, einschließlich des Dateninhalts. Dadurch können Sie auswählen, welche Ereignisse auf der Grundlage von Inhalten der Telemetrienachricht übermittelt werden sollen. Beispiele hierfür finden Sie unter [erweiterte Filterung](../event-grid/event-filtering.md#advanced-filtering).

Bei nicht telemetriebezogenen Ereignissen wie „DeviceConnected“, „DeviceDisconnected“, „DeviceCreated“ und „DeviceDeleted“ kann die Event Grid-Filterung beim Erstellen des Abonnements verwendet werden. Bei Telemetrieereignissen können Benutzer – zusätzlich zum Filtern in Event Grid – über die Abfrage des Nachrichtenroutings auch nach Gerätezwillingen, Nachrichteneigenschaften und Textkörper filtern. Wir erstellen in IoT Hub eine Standard-[Route](iot-hub-devguide-messages-d2c.md), die auf Ihrem Event Grid-Abonnement für Gerätetelemetrie basiert. Diese einzelne Route kann alle Ihre Event Grid-Abonnements verarbeiten. Wenn Sie Nachrichten vor dem Senden von Telemetriedaten filtern möchten, können Sie Ihre [Routingabfrage](iot-hub-devguide-routing-query-syntax.md) aktualisieren. Beachten Sie, dass die Routingabfrage nur auf einen Nachrichtenkörper im JSON-Format angewendet werden kann.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Beschränkungen bei den Ereignissen „Gerät verbunden“ und „Gerät getrennt“

Um die Ereignisse „Gerät verbunden“ und „Gerät getrennt“ zu erhalten, müssen Sie den D2C-Link oder den C2D-Link für Ihr Gerät öffnen. Wenn Ihr Gerät das MQTT-Protokoll verwendet, hält IoT Hub den C2D-Link geöffnet. Bei AMQP können Sie den C2D-Link öffnen, indem Sie die [ReceiveAsync-API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) aufrufen.

Der D2C-Link ist offen, wenn Sie Telemetriedaten senden. Wenn die Geräteverbindung instabil ist, das Gerät also häufig verbunden und getrennt wird, wird nicht jeder einzelne Verbindungsstatus gesendet. Der Verbindungsstatus, für den einmal pro Minute eine Momentaufnahme erstellt wird, wird aber veröffentlicht. Bei einem Ausfall von IoT Hub wird der Geräteverbindungsstatus veröffentlicht, sobald der Ausfall beendet ist. Wenn das Gerät während dieses Ausfalls getrennt wird, wird das Ereignis „Gerät getrennt“ innerhalb von 10 Minuten veröffentlicht.

## <a name="tips-for-consuming-events"></a>Tipps zum Nutzen von Ereignissen

Anwendungen, die IoT Hub-Ereignisse behandeln, sollten diesen empfohlenen Methoden entsprechen:

* Mehrere Abonnements können zum Weiterleiten von Ereignissen an denselben Ereignishandler konfiguriert werden; darum dürfen Sie nicht davon ausgehen, dass Ereignisse aus einer bestimmten Quelle stammen. Überprüfen Sie immer das Nachrichtenthema, um sicherzustellen, dass es von einem IoT Hub stammt, den Sie erwarten.

* Gehen Sie nicht davon aus, dass alle Ereignisse, die Sie erhalten, den Typen entsprechen, die Sie erwarten. Überprüfen Sie vor der Verarbeitung der Nachricht immer den eventType.

* Nachrichten können in falscher Reihenfolge oder nach einer Verzögerung eingehen. Über das Feld „ETag“ können Sie erfahren, ob Ihre Informationen zu Objekten für Ereignisse vom Typ „DeviceCreated“ (Gerät erstellt) oder „DeviceDeleted“ (Gerät gelöscht) auf dem neuesten Stand sind.

## <a name="next-steps"></a>Nächste Schritte

* [Ausprobieren des Tutorials zu IoT Hub-Ereignissen](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Erfahren Sie, wie Sie Ereignisse im Zusammenhang mit der Herstellung und Trennung von Geräteverbindungen sortieren.](iot-hub-how-to-order-connection-state-events.md)

* [Weitere Informationen zu Event Grid](../event-grid/overview.md)

* [Vergleichen der Unterschiede zwischen dem Routing von IoT Hub-Ereignissen und Nachrichten](iot-hub-event-grid-routing-comparison.md)
