---
title: Azure Event Grid-Ereignisschema für Azure Maps
description: Beschreibt die Eigenschaften und das Schema, die für Azure Maps-Ereignisse im Azure Event Grid verfügbar sind
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861853"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure Event Grid-Ereignisschema für Azure Maps

In diesem Artikel werden die Eigenschaften und das Schema für Azure Maps-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Ein Azure Maps-Konto gibt die folgenden Ereignistypen aus:

| Ereignistypen | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Wird ausgelöst, wenn die empfangenen Koordinaten sich von außerhalb eines bestimmten Geofence nach innerhalb bewegt haben |
| Microsoft.Maps.GeofenceExited | Wird ausgelöst, wenn die empfangenen Koordinaten sich von innerhalb eines bestimmten Geofence nach außerhalb bewegt haben |
| Microsoft.Maps.GeofenceResult | Wird jedes Mal ausgelöst, wenn eine Geofencingabfrage ein Ergebnis zurückgibt, unabhängig vom Status |

## <a name="event-examples"></a>Ereignisbeispiele

Das folgende Beispiel zeigt das Schema eines **GeofenceEntered**-Ereignisses

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

Das folgende Beispiel zeigt das Schema für **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="event-properties"></a>Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | object | Geofencing-Ereignisdaten. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| apiCategory | Zeichenfolge | API-Kategorie des Ereignisses. |
| apiName | Zeichenfolge | API-Name des Ereignisses. |
| issues | object | Listet während der Verarbeitung aufgetretene Probleme auf. Wenn Probleme zurückgegeben werden, werden mit der Antwort keine Geometrien zurückgegeben. |
| responseCode | number | HTTP-Antwortcode |
| Geometrien | object | Listet die Zaungeometrien auf, die die Koordinatenposition enthalten oder sich mit dem searchBuffer überschneiden, der die Position umgibt. |

Das Fehlerobjekt wird zurückgegeben, wenn in der Maps-API ein Fehler auftritt. Das Fehlerobjekt hat die folgenden Eigenschaften:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| error | ErrorDetails |Dieses Objekt wird zurückgegeben, wenn in der Maps-API ein Fehler auftritt.  |

Das ErrorDetails-Objekt wird zurückgegeben, wenn in der Maps-API ein Fehler auftritt. ErrorDetails oder das Objekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| code | Zeichenfolge | Der HTTP-Statuscode. |
| message | Zeichenfolge | Sofern verfügbar eine lesbare Beschreibung des Fehlers. |
| innererror | InnerError | Sofern verfügbar ein Objekt, das dienstspezifische Informationen zum Fehler enthält. |

InnerError ist ein Objekt, das dienstspezifische Informationen zum Fehler enthält. Das InnerError-Objekt weist die folgenden Eigenschaften auf: 

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| code | Zeichenfolge | Die Fehlermeldung. |

Das geometries-Objekt listet die IDs der Geofences auf, die bezogen auf die übermittelte Benutzerzeit in der Anforderung abgelaufen sind. Das geometries-Objekt enthält Geometrieelemente mit den folgenden Eigenschaften: 

| Eigenschaft | Typ | BESCHREIBUNG |
|:-------- |:---- |:----------- |
| deviceid | Zeichenfolge | Die ID des Geräts. |
| distance | Zeichenfolge | <p>Die Entfernung von der Koordinate zur nächstgelegenen Grenze des Geofence. Positiv bedeutet, dass die Koordinate sich außerhalb des Geofence befindet. Wenn sich die Koordinate außerhalb des Geofence befindet, aber mehr als den Wert von searchBuffer von der nächstgelegenen Geofencegrenze entfernt ist, beträgt der Wert 999. Negativ bedeutet, dass die Koordinate sich innerhalb des Geofence befindet. Wenn sich die Koordinate innerhalb des Polygons befindet, aber mehr als den Wert von searchBuffer von der nächstgelegenen Geofencegrenze entfernt ist, beträgt der Wert -999. Der Wert 999 bedeutet, dass mit großer Zuversicht davon ausgegangen werden kann, dass die Koordinate außerhalb des Geofence liegt. Der Wert -999 bedeutet, dass mit großer Zuversicht davon ausgegangen werden kann, dass die Koordinate innerhalb des Geofence liegt.<p> |
| geometryid |Zeichenfolge | Die eindeutige ID bezeichnet die Geofencegeometrie. |
| nearestlat | number | Breitengrad des nächstgelegenen Punkts der Geometrie. |
| nearestlon | number | Längengrad des nächstgelegenen Punkts der Geometrie. |
| udId | Zeichenfolge | Die eindeutige ID, die vom Benutzeruploaddienst beim Hochladen eines Geofence zurückgegeben wird. Ist in der Geofencing-Post-API nicht enthalten. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Listen der Geometrie-ID des Geofence, der bezogen auf die in der Anforderung übermittelte Benutzerzeit abgelaufen ist. |
| geometries | geometries[] |Listet die Zaungeometrien auf, die die Koordinatenposition enthalten oder sich mit dem searchBuffer überschneiden, der die Position umgibt. |
| invalidPeriodGeofenceGeometryId | string[]  | Listen der Geometrie-ID des Geofence, der sich bezogen auf die in der Anforderung übermittelte Benutzerzeit in einem ungültigen Zeitraum befindet. |
| isEventPublished | boolean | "True" wird wenn mindestens ein Ereignis im Azure Maps-Ereignisabonnenten veröffentlicht wurde, "false", wenn kein Ereignis im Azure Maps-Ereignisabonnenten veröffentlicht wurde. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).