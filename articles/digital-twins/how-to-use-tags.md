---
title: Hinzufügen von Tags zu digitalen Zwillingen
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie Tags für digitale Zwillinge implementieren.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c2620b52c426871b0ec85e3db237be2d373d42f1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458698"
---
# <a name="add-tags-to-digital-twins"></a>Hinzufügen von Tags zu digitalen Zwillingen 

Sie können das Konzept der Tags verwenden, um Ihre digitalen Zwillinge genauer zu anzugeben und zu kategorisieren. Insbesondere möchten Benutzer möglicherweise Tags aus vorhandenen Systemen wie etwa [Haystack-Tags](https://project-haystack.org/doc/TagModel) in ihren Azure Digital Twins-Instanzen replizieren. 

In diesem Artikel werden Muster beschrieben, die zum Implementieren von Tags für digitale Zwillinge verwendet werden können.

Tags werden zuerst als Eigenschaften innerhalb des [Modells](concepts-models.md) hinzugefügt, das einen digitalen Zwilling beschreibt. Diese Eigenschaft wird dann für den Zwilling festgelegt, wenn dieser auf der Grundlage des Modells erstellt wird. Danach können die Tags in [Abfragen](concepts-query-language.md) verwendet werden, um die Zwillinge zu identifizieren und zu filtern.

## <a name="marker-tags"></a>Markertags 

Bei einem **Markertag** handelt es sich um eine einfache Zeichenfolge, die zum Markieren oder Kategorisieren eines digitalen Zwillings (z. B. „blue“ oder „red“) verwendet wird. Bei dieser Zeichenfolge handelt es sich um den Namen des Tags. Markertags haben keinen aussagekräftigen Wert – das Tag ist allein durch sein Vorhandensein (oder Fehlen) von Bedeutung. 

### <a name="add-marker-tags-to-model"></a>Hinzufügen eines Markertags zu einem Modell 

Markertags werden als [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)-Zuordnung zwischen `string` und `boolean` modelliert. Der boolesche `mapValue` wird ignoriert, da nur das Vorhandensein des Tags wichtig ist. 

Im Folgenden finden Sie einen Auszug aus einem Zwillingsmodell, mit dem ein Markertag als Eigenschaft implementiert wird:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "boolean"
    }
  }
}
```

### <a name="add-marker-tags-to-digital-twins"></a>Hinzufügen von Markertags zu digitalen Zwillingen

Nachdem die `tags`-Eigenschaft Teil des Modells des digitalen Zwillings ist, können Sie das Markertag im digitalen Zwilling festlegen, indem Sie den Wert dieser Eigenschaft festlegen. 

Im Folgenden finden Sie ein Beispiel, bei dem der Marker `tags` für drei Zwillinge aufgefüllt wird:

```csharp
entity-01: "tags": { "red": true, "round": true } 
entity-02: "tags": { "blue": true, "round": true } 
entity-03: "tags": { "red": true, "large": true } 
```

### <a name="query-with-marker-tags"></a>Abfragen mit Markertags

Nachdem digitalen Zwillingen Tags hinzugefügt wurden, können die Tags zum Filtern der Zwillinge in Abfragen verwendet werden. 

Im Folgenden finden Sie eine Abfrage, mit der alle Zwillinge abgerufen werden, die mit „red“ getaggt sind: 

```sql
SELECT * FROM digitaltwins WHERE is_defined(tags.red) 
```

Tags können auch zu komplexeren Abfragen kombiniert werden. Im Folgenden finden Sie eine Abfrage, mit der alle Zwillinge abgerufen werden, die rund und nicht rot sind: 

```sql
SELECT * FROM digitaltwins WHERE NOT is_defined(tags.red) AND is_defined(tags.round) 
```

## <a name="value-tags"></a>Werttags 

Bei einem **Werttag** handelt es sich um ein Schlüssel-Wert-Paar, das verwendet wird, um einem Tag einen Wert zuzuweisen, z. B. `"color": "blue"` oder `"color": "red"`. Nachdem ein Werttags erstellt wurde, kann es durch Ignorieren des Tagwerts auch als Markertag verwendet werden. 

### <a name="add-value-tags-to-model"></a>Hinzufügen eines Werttags zu einem Modell 

Werttags werden als [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)-Zuordnung zwischen `string` und `string` modelliert. Sowohl `mapKey` als auch `mapValue` sind von Bedeutung. 

Im Folgenden finden Sie einen Auszug aus einem Zwillingsmodell, mit dem ein Werttag als Eigenschaft implementiert wird:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "string"
    }
  }
} 
```

### <a name="add-value-tags-to-digital-twins"></a>Hinzufügen von Werttags zu digitalen Zwillingen

Wie ein Markertag können Sie auch das Werttag in einem digitalen Zwilling festlegen, indem Sie den Wert der `tags`-Eigenschaft über das Modell festlegen. Wenn Sie ein Werttag als Markertag verwenden möchten, können Sie das `tagValue`-Feld auf den leeren Zeichenfolgenwert (`""`) festlegen. 

Im Folgenden finden Sie ein Beispiel, bei dem der Wert `tags` für drei Zwillinge aufgefüllt wird:

```csharp
entity-01: "tags": { "red": "", "size": "large" } 
entity-02: "tags": { "purple": "", "size": "small" } 
entity-03: "tags": { "red": "", "size": "small" } 
```

In diesem Beispiel werden `red` und `purple` als Markertags verwendet.

### <a name="query-with-value-tags"></a>Abfragen mit Werttags

Wie Markertags können Sie auch Werttags zum Filtern von Zwillingen in Abfragen verwenden. Sie können Werttags und Markertags auch zusammen verwenden.

Im obigen Beispiel wird `red` als Markertag verwendet. Im Folgenden finden Sie eine Abfrage, mit der alle Zwillinge abgerufen werden, die mit „red“ getaggt sind: 

```sql
SELECT * FROM digitaltwins WHERE is_defined(tags.red) 
```

Im Folgenden finden Sie eine Abfrage, mit der alle Entitäten abgerufen werden, die klein (Werttag) und nicht rot sind: 

```sql
SELECT * FROM digitaltwins WHERE NOT is_defined(tags.red) AND tags.size = 'small' 
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Entwerfen und Verwalten von Modellen digitaler Zwillinge:
* [*Verwenden Verwalten von Azure Digital Twins-Modellen*](how-to-manage-model.md)

Erfahren Sie mehr über das Abfragen des Zwillingsgraphen:
* [*Verwenden Abfragen des Zwillingsgraphen*](how-to-query-graph.md)