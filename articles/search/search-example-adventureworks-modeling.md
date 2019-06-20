---
title: 'Beispiel: Modellieren der AdventureWorks Inventory-Datenbank – Azure Search'
description: Es wird beschrieben, wie Sie relationale Daten modellieren und diese in ein vereinfachtes Dataset transformieren, um in Azure Search die Indizierung und die Volltextsuche zu ermöglichen.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61291888"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Beispiel: Modellieren der AdventureWorks Inventory-Datenbank für Azure Search

Das Modellieren von strukturierten Datenbankinhalten mit dem Ziel, einen effizienten Suchindex zu erhalten, ist meist keine einfache Aufgabe. Zusätzlich zur Zeitplanung und Änderungsverwaltung stellt es eine Herausforderung dar, Quellzeilen aus dem Zustand der Einbindung in die Tabelle so zu denormalisieren, dass daraus für die Suche geeignete Entitäten werden. In diesem Artikel werden die online verfügbaren AdventureWorks-Beispieldaten verwendet, um Fälle zu verdeutlichen, die beim Übergang von der Datenbank zur Suche häufiger eintreten. 

## <a name="about-adventureworks"></a>Informationen zu AdventureWorks

Wenn Sie eine SQL Server-Instanz nutzen, sind Sie ggf. bereits mit der AdventureWorks-Beispieldatenbank vertraut. Unter den Tabellen dieser Datenbank sind fünf Tabellen mit Produktinformationen.

+ **ProductModel**: Name
+ **Product**: Name, Farbe, Kosten, Größe, Gewicht, Bild, Kategorie (jede Zeile ist mit einem bestimmten ProductModel verknüpft)
+ **ProductDescription**: Beschreibung
+ **ProductModelProductDescription**: Gebietsschema (in jeder Zeile wird ein ProductModel mit einer bestimmten ProductDescription für eine bestimmte Sprache verknüpft)
+ **ProductCategory**: Name, übergeordnete Kategorie

Hier besteht die Aufgabe darin, alle diese Daten in einem vereinfachten Rowset zusammenzufassen, das von einem Suchindex erfasst werden kann. 

## <a name="considering-our-options"></a>Mögliche Optionen

Ein etwas naiver Ansatz würde darin bestehen, alle Zeilen der Tabelle „Product“ (verknüpft, falls zutreffend) zu indizieren, da diese Tabelle über die präzisesten Informationen verfügt. Bei diesem Ansatz wird der Suchindex aber für als doppelt wahrgenommene Einträge eines Resultsets geöffnet. Das Modell „Road-650“ ist beispielsweise in zwei Farben und sechs Größen erhältlich. Eine Abfrage nach „road bikes“ ergibt dann vor allem zwölf Instanzen desselben Modells, das nur anhand der Größe und Farbe variiert. Die anderen sechs Modelle von Straßenfahrrädern erscheinen in den Suchergebnissen dann erst weit hinten, und zwar auf Seite 2.

  ![Liste mit Produkten](./media/search-example-adventureworks/products-list.png "Liste mit Produkten")
 
Beachten Sie, dass das Modell „Road-650“ über zwölf Optionen verfügt. 1:n-Entitätszeilen werden im Suchindex am besten als Felder mit mehreren Werten oder Felder mit vorab aggregierten Werten dargestellt.

Das Lösen dieses Problems ist schwieriger als das Verschieben des Zielindex in die Tabelle „ProductModel“. Hierbei würden die wichtigen Daten in der Tabelle „Product“ ignoriert werden, die in den Suchergebnissen aber trotzdem enthalten sein sollten.

## <a name="use-a-collection-data-type"></a>Verwenden eines „Collection“-Datentyps

Der „richtige Ansatz“ ist die Nutzung eines Suchschemafeatures, das im Datenbankmodell nicht über eine direkte Entsprechung verfügt: **Collection(Edm.String)** . Ein Collection-Datentyp wird verwendet, wenn Sie nicht über eine sehr lange (einzelne) Zeichenfolge verfügen, sondern über eine Liste mit individuellen Zeichenfolgen. Wenn Sie Tags oder Schlüsselwörter nutzen, würden Sie für dieses Feld einen Collection-Datentyp verwenden.

Indem Sie Felder mit mehreren Werten vom Typ **Collection(Edm.String)** für „color“, „size“ und „image“ definieren, werden die Hilfsinformationen für die Facettierung und Filterung beibehalten, ohne dass im Index doppelte Einträge auftreten. Wenden Sie entsprechend Aggregatfunktionen auf die numerischen Felder von „Product“ an, und indizieren Sie **minListPrice** – anstatt **listPrice** für jedes einzelne Produkt.

Bei einem Index mit diesen Strukturen würde eine Suche nach „mountain bikes“ die Anzeige von einzelnen Fahrradmodellen ergeben, während wichtige Metadaten wie Farbe, Größe und niedrigster Preis erhalten bleiben. Der folgende Screenshot enthält eine entsprechende Darstellung.

  ![Beispiel für die Suche nach Mountainbikes](./media/search-example-adventureworks/mountain-bikes-visual.png "Beispiel für die Suche nach Mountainbikes")

## <a name="use-script-for-data-manipulation"></a>Verwenden eines Skripts für die Datenbearbeitung

Leider lässt sich diese Art der Modellierung allein durch SQL-Anweisungen nicht ohne Weiteres erreichen. Verwenden Sie stattdessen ein einfaches NodeJS-Skript, um die Daten zu laden und dann den für die Suche geeigneten JSON-Entitäten zuzuordnen.

Die endgültige Zuordnung von Datenbank und Suche sieht wie folgt aus:

+ model (Edm.String: searchable, filterable, retrievable) aus „ProductModel.Name“
+ description_en (Edm.String: searchable) aus „ProductDescription“ für das Modell mit culture=’en’
+ color (Collection(Edm.String): searchable, filterable, facetable, retrievable): eindeutige Werte aus „Product.Color“ für das Modell
+ size (Collection(Edm.String): searchable, filterable, facetable, retrievable): eindeutige Werte aus „Product.Size“ für das Modell
+ image (Collection(Edm.String): retrievable): eindeutige Werte aus „Product.ThumbnailPhoto“ für das Modell
+ minStandardCost (Edm.Double: filterable, facetable, sortable, retrievable): Aggregatminimum aller „Product.StandardCost“-Einträge für das Modell
+ minListPrice (Edm.Double: filterable, facetable, sortable, retrievable): Aggregatminimum aller „Product.ListPrice“-Einträge für das Modell
+ minWeight (Edm.Double: filterable, facetable, sortable, retrievable): Aggregatminimum aller „Product.Weight“-Einträge für das Modell
+ products (Collection(Edm.String): searchable, filterable, retrievable): eindeutige Werte aus „Product.Name“ für das Modell

Nachdem die Tabelle „ProductModel“ mit „Product“ und „ProductDescription“ verknüpft wurde, können Sie [lodash](https://lodash.com/) (oder Linq in C#) verwenden, um das Resultset schnell zu transformieren:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Der sich ergebende JSON-Code sieht wie folgt aus:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Abschließend ist hier die SQL-Abfrage zum Zurückgeben des anfänglichen Recordsets angegeben. Ich habe das npm-Modul [mssql](https://www.npmjs.com/package/mssql) verwendet, um die Daten in meine NodeJS-App zu laden.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Beispiel: Multi-level facet taxonomies in Azure Search](search-example-adventureworks-multilevel-faceting.md) (Beispiel: Facet-Taxonomien mit mehreren Ebenen in Azure Search)


