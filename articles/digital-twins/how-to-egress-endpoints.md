---
title: Ausgang und Endpunkte in Azure Digital Twins | Microsoft-Dokumentation
description: Leitfaden für die Erstellung von Endpunkten mit Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: alinast
ms.openlocfilehash: 478fe1859dd9067e8097df0384657793602c1378
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071462"
---
# <a name="egress-and-endpoints"></a>Ausgang und Endpunkte

Bei *Endpunkten* von Azure Digital Twins handelt es sich um eine Nachricht oder einen Ereignisbroker in einem Azure-Abonnement eines Benutzers. Ereignisse und Nachrichten können an Azure Event Hubs, Azure Event Grid und Azure Service Bus-Themen gesendet werden.

Ereignisse werden entsprechend den vordefinierten Routingeinstellungen an Endpunkte weitergeleitet. Die Benutzer legen fest, welche *Ereignistypen* jeder Endpunkt empfangen kann.

Weitere Informationen zu Ereignissen, Routing und Ereignistypen finden Sie unter [Routing events and messages in Azure Digital Twins (Weiterleiten von Ereignissen und Nachrichten)](./concepts-events-routing.md).

## <a name="events"></a>Events

Ereignisse werden von IoT-Objekten wie Geräte und Sensoren zur Verarbeitung durch Azure-Nachrichtenbroker und -Ereignisbroker gesendet. Ereignisse werden durch das folgende [Azure Event Grid-Ereignisschema](../event-grid/event-schema.md) definiert.

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Attribut | Type | BESCHREIBUNG |
| --- | --- | --- |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| data | object | Die für den Ressourcenanbieter spezifischen Ereignisdaten. |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |
| topic | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |

Weitere Informationen zum Event Grid-Ereignisschema:

- Lesen Sie den Artikel [Azure Event Grid-Ereignisschema](../event-grid/event-schema.md).
- Lesen Sie die Referenz zur [EventGridEvent-Schnittstelle](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Ereignistypen

Ereignistypen klassifizieren die Art des Ereignisses und werden im Feld **eventType** festgelegt. Die folgende Liste gibt verfügbare Ereignistypen an:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Die Ereignisformate für jeden Ereignistypen werden in den folgenden Unterabschnitten näher beschrieben.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** gilt für Änderungen des Graphen. Die **subject**-Eigenschaft gibt den Typ des betroffenen Objekts an. Die folgenden Objekttypen können dieses Ereignis auslösen:

- Gerät
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Bericht
- RoleDefinition
- Sensor
- SensorBlobMetadata
- SensorExtendedProperty
- Leerzeichen
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- System
- Benutzer
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Beispiel

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wert | Ersetzen durch |
| --- | --- |
| YOUR_TOPIC_NAME | Den Namen Ihres angepassten Themas |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** ist ein Ereignis, das von einer benutzerdefinierten Funktion (User-Defined Function, UDF) gesendet wurde.
  
> [!IMPORTANT]  
> Dieses Ereignis muss explizit von der benutzerdefinierten Funktion selbst gesendet werden.

#### <a name="example"></a>Beispiel

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wert | Ersetzen durch |
| --- | --- |
| YOUR_TOPIC_NAME | Den Namen Ihres angepassten Themas |

### <a name="sensorchange"></a>SensorChange

**SensorChange** ist eine Aktualisierung des Zustands eines Sensors auf der Grundlage von Telemetriedatenänderungen.

#### <a name="example"></a>Beispiel

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wert | Ersetzen durch |
| --- | --- |
| YOUR_TOPIC_NAME | Den Namen Ihres angepassten Themas |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** ist eine Aktualisierung des Zustands eines Gebäudebereichs auf der Grundlage von Telemetriedatenänderungen.

#### <a name="example"></a>Beispiel

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wert | Ersetzen durch |
| --- | --- |
| YOUR_TOPIC_NAME | Den Namen Ihres angepassten Themas |

### <a name="devicemessage"></a>DeviceMessage

Mit **DeviceMessage** können Sie eine **EventHub**-Verbindung angeben, an die Telemetrierohereignisse ebenfalls aus Azure Digital Twins weitergeleitet werden können.

> [!NOTE]
> - **DeviceMessage** kann nur mit **EventHub** kombiniert werden. Es ist nicht möglich, **DeviceMessage** mit einem der anderen Ereignistypen zu kombinieren.
> - Sie können nur einen Endpunkt mit der Kombination der Typen **EventHub** oder **DeviceMessage** angeben.

## <a name="configure-endpoints"></a>Endpunkte konfigurieren

Endpunktverwaltung erfolgt über die Endpunkte-API.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Die folgenden Beispiele veranschaulichen, wie die unterstützten Endpunkte konfiguriert werden.

>[!IMPORTANT]
> Achten Sie auf das Attribut **eventTypes**. Es definiert die Ereignistypen, die vom Endpunkt bearbeitet werden, und bestimmt sein Routing.

Eine authentifizierte HTTP POST-Anforderung mit

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Ereignistypen für das Weiterleiten an Service Bus: **SensorChange**, **SpaceChange** und **TopologyOperation**.

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Wert | Ersetzen durch |
    | --- | --- |
    | YOUR_NAMESPACE | Der Namespace Ihres Endpunkts |
    | YOUR_PRIMARY_KEY | Die primäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird |
    | YOUR_SECONDARY_KEY | Die sekundäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird |
    | YOUR_TOPIC_NAME | Den Namen Ihres angepassten Themas |

- Ereignistypen für das Weiterleiten an Event Grid: **SensorChange**, **SpaceChange** und **TopologyOperation**.

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Wert | Ersetzen durch |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Die primäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird|
    | YOUR_SECONDARY_KEY | Die sekundäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird |
    | YOUR_TOPIC_NAME | Den Namen Ihres angepassten Themas |

- Ereignistypen für das Weiterleiten an Event Hubs: **SensorChange**, **SpaceChange** und **TopologyOperation**.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Wert | Ersetzen durch |
    | --- | --- |
    | YOUR_NAMESPACE | Der Namespace Ihres Endpunkts |
    | YOUR_PRIMARY_KEY | Die primäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird |
    | YOUR_SECONDARY_KEY | Die sekundäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird |
    | YOUR_EVENT_HUB_NAME | Der Name Ihres Event Hubs |

- Ereignistyp für das Weiterleiten an Event Hubs: **DeviceMessage**. `EntityPath` muss in **connectionString** enthalten sein:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Wert | Ersetzen durch |
    | --- | --- |
    | YOUR_NAMESPACE | Der Namespace Ihres Endpunkts |
    | YOUR_PRIMARY_KEY | Die primäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird |
    | YOUR_SECONDARY_KEY | Die sekundäre Verbindungszeichenfolge, die zur Authentifizierung verwendet wird |
    | YOUR_EVENT_HUB_NAME | Der Name Ihres Event Hubs |

> [!NOTE]  
> Nach dem Erstellen eines neuen Endpunkts kann es fünf bis zehn Minuten dauern, bis erstmals Ereignisse auf dem Endpunkt empfangen werden.

## <a name="primary-and-secondary-connection-keys"></a>Primärer und sekundärer Verbindungsschlüssel

Wenn ein primärer Verbindungsschlüssel nicht länger autorisiert ist, versucht das System automatisch, den sekundären Verbindungsschlüssel zu verwenden. Dies bietet eine Sicherung und eröffnet die Möglichkeit, den primären Schlüssel ungestört über die API-Endpunkte zu authentifizieren und zu aktualisieren.

Wenn sowohl der primäre als auch der sekundäre Verbindungsschlüssel nicht autorisiert sind, wechselt das System in eine exponentielle Backoff-Wartezeit von bis zu 30 Minuten. Ereignisse werden bei jeder ausgelösten Backoff-Wartezeit gelöscht.

Wenn sich das System in einem Backoff-Wartezustand befindet, kann es bis zu 30 Minuten dauern, bis eine Aktualisierung von Verbindungsschlüsseln über die Endpunkte-API wirksam wird.

## <a name="unreachable-endpoints"></a>Nicht erreichbare Endpunkte

Wenn ein Endpunkt nicht mehr erreichbar ist, wechselt das System in eine exponentielle Backoff-Wartezeit von bis zu 30 Minuten. Ereignisse werden bei jeder ausgelösten Backoff-Wartezeit gelöscht.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Azure Digital Twins-Swagger verwenden](how-to-use-swagger.md).

- Erfahren Sie mehr über das [Weiterleiten von Ereignissen und Nachrichten](concepts-events-routing.md) in Azure Digital Twins.
