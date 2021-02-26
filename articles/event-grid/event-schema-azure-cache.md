---
title: Azure Cache for Redis als Event Grid-Quelle
description: Hier werden die Eigenschaften beschrieben, die für Azure Cache for Redis-Ereignisse mit Azure Event Grid bereitgestellt werden.
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056856"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure Cache for Redis als Event Grid-Quelle

In diesem Artikel werden die Eigenschaften und das Schema für Azure Cache for Redis-Ereignisse beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). 

## <a name="event-grid-event-schema"></a>Event Grid-Ereignisschema

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Liste der Ereignisse für Azure Cache for Redis-REST-APIs

Diese Ereignisse werden ausgelöst, wenn ein Client durch Aufrufen von Azure Cache for Redis-REST-APIs einen Export-, Import- oder Skalierungsvorgang ausführt. Das Patchingereignis wird durch ein Redis-Update ausgelöst.

 |Ereignisname |BESCHREIBUNG|
 |----------|-----------|
 |**Microsoft.Cache.ExportRDBCompleted** |Wird ausgelöst, wenn Cachedaten exportiert werden. |
 |**Microsoft.Cache.ImportRDBCompleted** |Wird ausgelöst, wenn Cachedaten importiert werden. |
 |**Microsoft.Cache.PatchingCompleted** |Wird ausgelöst, wenn das Patchen abgeschlossen ist. |
 |**Microsoft.Cache.ScalingCompleted** |Wird ausgelöst, wenn das Skalieren abgeschlossen ist. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Der Inhalt einer Ereignisantwort

Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt, der über ein entsprechendes Abonnement verfügt.

In diesem Abschnitt wird jeweils anhand eines Beispiels gezeigt, wie diese Daten für die Azure Cache for Redis-Ereignisse aussehen:

### <a name="microsoftcachepatchingcompleted-event"></a>Ereignis „Microsoft.Cache.PatchingCompleted“

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Ereignis „Microsoft.Cache.ImportRDBCompleted“

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Ereignis „Microsoft.Cache.ExportRDBCompleted“

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft.Cache.ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | Objekt (object) | Azure Cache for Redis-Ereignisdaten. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| timestamp | Zeichenfolge | Der Zeitpunkt, an dem das Ereignis aufgetreten ist. |
| name | Zeichenfolge | Der Name des Ereignisses. |
| status | Zeichenfolge | Der Status des Ereignisses. Erfolgreich oder nicht erfolgreich. |


## <a name="quickstarts"></a>Schnellstarts

Wenn Sie Azure Cache for Redis-Ereignisse ausprobieren möchten, können Sie die folgenden Schnellstartartikel verwenden:

|Gewünschtes Tool:    |In diesem Artikel finden Sie weitere Informationen: |
|--|-|
|Azure-Portal    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit dem Azure-Portal](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit der Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).

