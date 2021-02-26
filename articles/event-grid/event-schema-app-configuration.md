---
title: Azure App Configuration als Event Grid-Quelle
description: In diesem Artikel wird beschrieben, wie Azure App Configuration als Event Grid-Ereignisquelle verwendet wird. Er enthält das Schema sowie Links zu Tutorials und Artikeln mit Vorgehensweisen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d305236e8408052be4be28ec003f4e545119fc59
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550673"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Azure App Configuration als Event Grid-Quelle
In diesem Artikel werden die Eigenschaften und das Schema für Azure App Configuration-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md). Außerdem erhalten Sie eine Liste mit Schnellstarts und Tutorials, die Azure App Configuration als Ereignisquelle verwenden.

## <a name="event-grid-event-schema"></a>Event Grid-Ereignisschema

### <a name="available-event-types"></a>Verfügbare Ereignistypen

Azure App Configuration gibt die folgenden Ereignistypen aus:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Wird ausgelöst, wenn ein Schlüssel-Wert-Paar erstellt oder ersetzt wird. |
| Microsoft.AppConfiguration.KeyValueDeleted | Wird ausgelöst, wenn ein Schlüssel-Wert-Paar gelöscht wird. |

### <a name="example-event"></a>Beispielereignis

Das folgende Beispiel zeigt das Schema eines Ereignisses aufgrund eines geänderten Schlüssel-Wert-Paars: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Das Schema für ein Ereignis aufgrund eines gelöschten Schlüssel-Wert-Paars sieht ähnlich aus: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
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
| data | Objekt (object) | App Configuration-Ereignisdaten. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| Schlüssel | Zeichenfolge | Der Schlüssel des Schlüssel-Wert-Paars, das geändert oder gelöscht wurde. |
| label | Zeichenfolge | Die Bezeichnung (sofern vorhanden) des Schlüssel-Wert-Paars, das geändert oder gelöscht wurde. |
| etag | Zeichenfolge | Für `KeyValueModified`: Das ETag des neuen Schlüssel-Wert-Paars. Für `KeyValueDeleted`: Das ETag des gelöschten Schlüssel-Wert-Paars. |

## <a name="tutorials-and-how-tos"></a>Tutorials und Vorgehensweisen

|Titel | BESCHREIBUNG |
|---------|---------|
| [Reacting to Azure App Configuration events](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) (Reagieren auf Azure App Configuration-Ereignisse) | Übersicht über die Integration von Azure App Configuration in Event Grid. |
| [Verwenden von Event Grid für Datenänderungsbenachrichtigungen](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Erfahren Sie, wie Sie Azure App Configuration-Ereignisabonnements verwenden, um Schlüssel-Wert-Änderungsereignisse an einen Webendpunkt zu senden. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
* Eine Einführung in das Arbeiten mit Azure App Configuration-Ereignissen finden Sie unter [Verwenden von Event Grid für Datenänderungsbenachrichtigungen](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 