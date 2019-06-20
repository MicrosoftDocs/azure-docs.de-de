---
title: Datenformat – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zum Datenformat in der KES-API (Knowledge Exploration Service).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815086"
---
# <a name="data-format"></a>Datenformat

Die Datendatei beschreibt die Liste der zu indizierenden Objekte.
Jede Zeile in der Datei spezifiziert die Attributwerte eines Objekts im [JSON-Format](https://json.org/) mit UTF-8-Codierung.
Zusätzlich zu den in [schema](SchemaFormat.md) definierten Attributen hat jedes Objekt ein optionales „logprob“-Attribut, das die relative Logarithmuswahrscheinlichkeit unter den Objekten angibt.
Wenn der Dienst Objekte in der Reihenfolge der abnehmenden Wahrscheinlichkeit zurückgibt, können wir „logprob“ verwenden, um die Rückgabereihenfolge der passenden Objekte anzuzeigen.
Bei einer Wahrscheinlichkeit *p* zwischen 0 und 1 kann die entsprechende Logarithmuswahrscheinlichkeit als log(*p*) berechnet werden, wobei „log()“ die natürliche Logarithmusfunktion ist.
Wenn kein Wert für „logprob“ angegeben ist, wird der Standardwert 0 verwendet.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Bei String-, GUID- und Blob-Attributen wird der Wert als JSON-Zeichenfolge in Anführungszeichen dargestellt.  Für numerische Attribute (Int32, Int64, Double) wird der Wert als eine JSON-Zahl dargestellt.  Für zusammengesetzte Attribute ist der Wert ein JSON-Objekt, das die untergeordneten Attributwerte angibt.  Um das Indizieren zu beschleunigen, sortieren Sie die Objekte nach sinkender Wahrscheinlichkeit.

Ein Attribut kann allgemein 0 oder mehr Werte haben.  Wenn ein Attribut keinen Wert aufweist, löschen wir es einfach aus dem JSON-Objekt.  Wenn ein Attribut 2 oder mehr Werte aufweist, können wir das Attributwertpaar im JSON-Objekt wiederholen.  Alternativ können wir dem Attribut ein JSON-Array mit mehreren Werten zuweisen.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
