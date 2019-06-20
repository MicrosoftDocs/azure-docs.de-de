---
title: 'Beispiel: Facets auf mehreren Ebenen – Azure Search'
description: Informationen zum Erstellen von Facetingstrukturen für Taxonomien mit mehreren Ebenen zum Erstellen einer geschachtelten Navigationsstruktur, die Sie in Anwendungsseiten einbeziehen können.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: chstone
ms.openlocfilehash: e17a91a35b69102e4e0ac6025559bbc32e71d8fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024128"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Beispiel: Facets auf mehreren Ebenen in Azure Search

Azure Search-Schemas unterstützen nicht explizit Taxonomiekategorien mit mehreren Ebenen, aber Sie können sie annähern, indem Sie Inhalt vor der Indizierung bearbeiten und dann die Ergebnisse einer speziellen Behandlung unterziehen. 

## <a name="start-with-the-data"></a>Der Anfang mit den Daten

Das Beispiel in diesem Artikel baut auf einem der vorherigen Beispiele auf – [Modellieren der AdventureWorks Inventory-Datenbank für Azure Search](search-example-adventureworks-modeling.md) – um das Faceting mit mehreren Ebenen in Azure Search zu veranschaulichen.

AdventureWorks verfügt über eine einfache Taxonomie mit zwei Ebenen mit einer Über-/Unterordnungsbeziehung. Für Taxonomietiefen dieser Struktur mit fester Länge kann die Taxonomie mit einer einfachen SQL-Join-Abfrage gruppiert werden:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Abfrageergebnisse](./media/search-example-adventureworks/prod-query-results.png "Abfrageergebnisse")

## <a name="indexing-to-a-collection-field"></a>Indizierung nach einem Collection-Feld

Erstellen Sie in dem Index, der diese Struktur enthält, im Azure Search-Schema zum Speichern dieser Daten ein **Collection(Edm.String)** -Feld, um sicherzustellen, dass die Feldattribute durchsuchbar, filterbar, facettierbar und abrufbar vorhanden sind.

Senden Sie nun beim Indizieren von Inhalten, die sich auf eine bestimmte Taxonomiekategorie beziehen, die Taxonomie als Array mit Text aus jeder Ebene. Senden Sie z.B. für eine Entität mit `ProductCategoryId = 5 (Mountain Bikes)` das Feld als `[ "Bikes", "Bikes|Mountain Bikes"]`.

Beachten Sie die Aufnahme der übergeordneten Kategorie „Bikes“ in den untergeordneten Kategoriewert „Mountain Bikes“. In jede Unterkategorie sollten deren gesamter Pfad relativ zum Stammelement eingebettet werden. Der senkrechte Strich als Trennzeichen ist beliebig, aber er muss konsistent verwendet werden und darf nicht im Quelltext vorkommen. Das Trennzeichen wird im Anwendungscode verwendet, um die Taxonomiestruktur auf der Basis von Facettenergebnissen zu rekonstruieren.

## <a name="construct-the-query"></a>Erstellen der Abfrage

Beziehen Sie bei der Ausgabe von Abfragen die folgende Facettenspezifikation mit ein (wobei die Taxonomie Ihr facettierbares Taxonomiefeld ist): `facet = taxonomy,count:50,sort:value`

Der Wert für die Anzahl muss hoch genug, um alle möglichen Taxonomiewerte zurückzugeben. Die AdventureWorks-Daten enthalten 41 verschiedene Taxonomiewerte, also ist `count:50` ausreichend.

  ![Facettierter Filter](./media/search-example-adventureworks/facet-filter.png "Facettierter Filter")

## <a name="build-the-structure-in-client-code"></a>Erstellen der Struktur im Clientcode

Rekonstruieren Sie die Taxonomiestruktur in Ihrem Clientanwendungscode durch Aufteilen jedes einzelnen Facettenwerts am senkrechten Strich.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

Mit dem **categories**-Objekt kann jetzt eine reduzierbare Taxonomiestruktur mit präziser Anzahl gerendert werden:

  ![Facettierter Filter mit mehreren Ebenen](./media/search-example-adventureworks/multi-level-facet.png "Facettierter Filter mit mehreren Ebenen")

 
Jeder Link in der Struktur sollte den verknüpften Filter anwenden. Beispiel:

+ **taxonomy/any**`(x:x eq 'Accessories')` gibt alle Dokumente in der Verzweigung „Accessories“ zurück
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')` gibt nur Dokumente mit einer Unterkategorie „Bike Racks“ unter der Verzweigung „Accessories“ zurück.

Dieses Verfahren skaliert, um komplexere Szenarien wie tiefere Taxonomiestrukturen abzudecken, und dupliziert Unterkategorien, die unter verschiedenen übergeordneten Kategorien auftreten (z.B. `Bike Components|Forks` und `Camping Equipment|Forks`).

> [!TIP]
> Die Geschwindigkeit der Abfrage wird von der Anzahl der zurückgegebenen Facetten beeinflusst. Um sehr große Taxonomiegruppen zu unterstützen, sollten Sie ein facettierbares Feld **Edm.String** für den Taxonomiewert der obersten Ebene für jedes Dokument hinzufügen. Wenden Sie dann das gleiche Verfahren wie oben an, aber führen Sie nur die „Sammlung-Facetten“-Abfrage durch (nach dem Stammtaxonomiefeld gefiltert), wenn der Benutzer einen Knoten der obersten Ebene erweitert. Reduzieren Sie alternativ, wenn kein 100%-Rückruf erforderlich ist, einfach die Anzahl der Facetten auf einen angemessenen Wert, und stellen Sie sicher, dass die Facetteneinträge nach Anzahl sortiert werden.

## <a name="see-also"></a>Weitere Informationen

[Beispiel: Modellieren der AdventureWorks Inventory-Datenbank für Azure Search](search-example-adventureworks-modeling.md)