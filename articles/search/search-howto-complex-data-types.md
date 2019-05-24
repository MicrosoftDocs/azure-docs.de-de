---
title: Modellieren komplexer Datentypen – Azure Search
description: Geschachtelte oder hierarchische Datenstrukturen lassen sich in einem Azure Search-Index mit den Datentypen „ComplexType“ und „Collections“ modellieren.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 10400b0342fbe8667b22fea82c6446713d019e0d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597340"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Gewusst wie: Modellieren komplexer Datentypen in Azure Search

Externe Datasets, die zum Auffüllen eines Azure Search-Index verwendet werden, weisen manchmal hierarchische oder geschachtelte Teilstrukturen auf. Beispiele können mehrere Standorte und Telefonnummern für einen einzelnen Kunden, mehrere Farben und Größen für eine einzelne SKU, mehrere Autoren für ein einzelnes Buch usw. enthalten. In der Modelliersprache werden diese Strukturen bisweilen als *komplexe Datentypen*, *zusammengesetzte Datentypen*, *verbundene Datentypen* oder *aggregierte Datentypen* bezeichnet. In der Terminologie von Azure Search ist ein komplexer Typ ein Feld, das untergeordnete Felder (Unterfelder) enthält, die selbst wieder einfach oder komplex sein können. Dies lässt sich mit einem strukturierten Datentyp in einer Programmiersprache vergleichen. Komplexe Felder können entweder einzelne Felder sein, die ein einzelnes Objekt im Dokument darstellen, oder eine Sammlung, die ein Array von Objekten darstellt.

In Azure Search werden komplexe Typen und Sammlungen nativ unterstützt. Mit allen diesen Typen können Sie nahezu jede geschachtelte JSON-Struktur in einem Azure Search-Index modellieren. In früheren Versionen von Azure Search-APIs konnten nur vereinfachte Rowsets importiert werden. In der neuesten Version kann der Index nun genauer den Quelldaten entsprechen. Mit anderen Worten: Wenn die Quelldaten komplexe Typen enthalten, kann auch der Index komplexe Typen enthalten.

Zum Einstieg empfiehlt sich das [Dataset „Hotels“](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), das Sie im Assistenten **Daten importieren** im Azure-Portal laden können. Im Assistenten werden komplexe Typen in der Quelle erkannt, und es wird basierend auf den erkannten Strukturen ein Indexschema vorgeschlagen.

> [!Note]
> Die Unterstützung für komplexe Typen ist in `api-version=2019-05-06` allgemein verfügbar. 
>
> Wenn Ihre Suchlösung auf früheren Problemumgehungen von vereinfachten Datasets in einer Sammlung aufbaut, sollten Sie den Index so ändern, dass er komplexe Typen enthält, wie sie in der neuesten API-Version unterstützt werden. Weitere Informationen zum Aktualisieren von API-Versionen finden Sie unter [Aktualisieren auf die neueste Version der REST-API](search-api-migration.md) oder [Aktualisieren auf die neueste Version des .NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Beispiel für eine komplexe Struktur

Das folgende JSON-Dokument besteht aus einfachen und komplexen Feldern. Komplexe Felder, z. B. `Address` und `Rooms`, enthalten Unterfelder. `Address` umfasst einen einzelnen Wertesatz für diese Unterfelder, da es sich um ein einzelnes Objekt im Dokument handelt. Im Gegensatz dazu umfasst `Rooms` mehrere Wertesätze für die zugehörigen Unterfelder, jeweils einen Satz für jedes Objekt in der Sammlung.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Erstellen komplexer Felder

Wie jede Indexdefinition können Sie ein Schema, das komplexe Typen enthält, im Portal, mit der [REST-API](https://docs.microsoft.com/rest/api/searchservice/create-index) oder mit dem [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) erstellen. 

Im folgenden Beispiel ist ein JSON-Indexschema mit einfachen Feldern, Sammlungen und komplexen Typen dargestellt. Beachten Sie, dass in einem komplexen Typ jedes Unterfeld einen Typ enthält und Attribute enthalten kann, so wie das auch bei übergeordneten Feldern der Fall ist. Das Schema entspricht den Beispieldaten oben. `Address` ist ein komplexes Feld, das keine Sammlung ist (ein Hotel weist eine Adresse auf). `Rooms` ist ein komplexes Sammlungsfeld (ein Hotel hat viele Zimmer).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true  },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Aktualisieren komplexer Felder

Alle [Neuindizierungsregeln](search-howto-reindex.md), die allgemein für Felder gelten, gelten auch für komplexe Felder. Das Anpassen einiger Hauptregeln oder das Hinzufügen eines Felds erfordern keine Indexneuerstellung, aber die meisten Änderungen erfordern dies.

### <a name="structural-updates-to-the-definition"></a>Strukturelle Aktualisierungen der Definition

Sie können einem komplexen Feld jederzeit neue Unterfelder hinzufügen, ohne dass eine Indexneuerstellung erforderlich ist. Beispielsweise ist es möglich, `Address` „ZipCode“ oder `Rooms` „Amenities“ hinzuzufügen, so wie ein Feld auf oberster Ebene einem Index hinzugefügt wird. Vorhandene Dokumente haben einen NULL-Wert für neue Felder, bis Sie diese Felder durch Aktualisieren Ihrer Daten explizit füllen.

Beachten Sie, dass in einem komplexen Typ jedes Unterfeld einen Typ enthält und Attribute enthalten kann, so wie das auch bei übergeordneten Feldern der Fall ist.

### <a name="data-updates"></a>Datenaktualisierungen

Die Aktualisierung vorhandener Dokumente in einem Index mit der Uploadaktion wird für komplexe und einfache Felder auf identische Weise durchgeführt, d. h., alle Felder werden ersetzt. Jedoch wird „merge“ (oder „mergeOrUpload“ beim Anwenden auf ein vorhandenes Dokument) nicht für alle Feldern gleich ausgeführt. Insbesondere können mit „merge“ Elemente in einer Sammlung nicht zusammengeführt werden. Dies gilt für Sammlungen von Grundtypen sowie für komplexe Sammlungen. Zum Aktualisieren einer Sammlung müssen Sie den vollständigen Sammlungswert abrufen, Änderungen vornehmen und dann die neue Sammlung in der Anforderung der Index-API einfügen.


## <a name="searching-complex-fields"></a>Durchsuchen komplexer Felder

Freiform-Suchausdrücke funktionieren bei komplexen Typen wie erwartet. Wenn ein durchsuchbares Feld oder Unterfeld an beliebiger Stelle in einem Dokument übereinstimmt, ist das Dokument selbst eine Übereinstimmung. 

Abfragen werden bei mehreren Begriffen und Operatoren differenzierter, und bei einigen Begriffen sind Feldnamen angegeben, wie das mit der [Lucene-Syntax](query-lucene-syntax.md) möglich ist. Mit der folgenden Abfrage wird beispielsweise versucht, zwei Begriffe, „Portland“ und „OR“, mit zwei Unterfeldern des Felds „Address“ zu vergleichen:

```json
search=Address/City:Portland AND Address/State:OR
```

Solche Abfragen sind für die Volltextsuche nicht korreliert (im Unterschied zu Filtern, bei denen Abfragen von Unterfeldern einer komplexen Sammlung mithilfe von „any“ oder „all“ korreliert werden können, wie z. B. bei einer korrelierten Unterabfrage in SQL). Das bedeutet, dass die Lucene-Abfrage oben Dokumente zurückgibt, die „Portland, Maine“ sowie „Portland, Oregon“ und andere Orte in Oregon zurückgibt. Dies liegt daran, dass jede Klausel mit allen Werten des angegebenen Felds im gesamten Dokument verglichen wird, es also kein Konzept eines „aktuellen Unterdokuments“ gibt. 

 

## <a name="selecting-complex-fields"></a>Auswählen komplexer Felder

Über den Parameter `$select` wird ausgewählt, welche Felder in den Suchergebnissen zurückgegeben werden. Um diesen Parameter zum Auswählen bestimmter Unterfelder eines komplexen Felds zu verwenden, fügen Sie das übergeordnete Feld und das Unterfeld getrennt durch einen Schrägstrich (`/`) ein.

```json
$select=HotelName, Address/City, Rooms/BaseRate
```

Felder müssen im Index als „Abrufbar“ markiert sein, wenn sie in den Suchergebnissen enthalten sein sollen. Nur die als „Abrufbar“ markierten Felder können in einer `$select`-Anweisung verwendet werden. 


## <a name="filter-facet-and-sort-complex-fields"></a>Filtern, Faceting und Sortieren komplexer Felder

Die für die Filterung und für feldbezogene Suchen verwendete [OData-Pfadsyntax](query-odata-filter-orderby-syntax.md) kann auch für das Faceting, die Sortierung und die Auswahl von Feldern in einer Suchanforderung verwendet werden. Für komplexe Typen gelten Regeln, mit denen gesteuert wird, welche Unterfelder als sortierbar oder facettierbar markiert werden können. 

### <a name="faceting-sub-fields"></a>Faceting von Unterfeldern 

Jedes Unterfeld kann als facettierbar markiert werden, mit Ausnahme von Feldern der Typen `Edm.GeographyPoint` und `Collection(Edm.GeographyPoint)`. 

Wenn für die Facetnavigationsstruktur eine Dokumentanzahl zurückgegeben wird, ist die Anzahl relativ zum übergeordneten Dokument (ein Hotel) und nicht relativ zu geschachtelten Dokumenten in einer komplexen Sammlung (Zimmer). Beispiel: Ein Hotel hat 20 Zimmer vom Typ „suite“. Bei diesem Facet-Parameter `facet=Rooms/Type` entspricht die Facet-Anzahl der Anzahl für das übergeordnete Dokument (Hotels) und nicht für zwischengeordnete Unterdokumente (Zimmer). 

### <a name="sorting-complex-fields"></a>Sortieren komplexer Felder

Sortiervorgänge gelten für Dokumente (Hotels) und nicht für Unterdokumente (Zimmer). Bei einer Sammlung von komplexen Typen, z. B. „Rooms“ (Zimmer), ist es wichtig, zu wissen, dass für „Rooms“ keinerlei Sortiervorgänge durchgeführt werden können. Sortiervorgänge können für keine Sammlung durchgeführt werden. 

Sortiervorgänge sind möglich, wenn Felder einwertig sind. Dabei kann es sich um einfache Felder oder um Unterfelder in einem komplexen Typ handeln. Der komplexe Typ `$orderby=Address/ZipCode` ist beispielsweise sortierbar, da es nur eine Postleitzahl pro Hotel gibt. 

Bei der Anpassung der Regeln zur Sortierung muss ein Indexfeld als filterbar und sortierbar markiert sein, damit es in einer `$orderby`-Anweisung verwendet werden kann. 

## <a name="next-steps"></a>Nächste Schritte

 Testen Sie das [Dataset „Hotels“](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) im Assistenten **Daten importieren**. Für den Zugriff auf die Daten benötigen Sie die in der Infodatei angegebenen Cosmos DB-Verbindungsinformationen. 
 
 Mit diesen Informationen erstellen Sie im ersten Schritt im Assistenten eine neue Azure Cosmos DB-Datenquelle. Später im Assistenten, auf der Seite für den Zielindex, wird ein Index mit komplexen Typen angezeigt. Erstellen und laden Sie diesen Index, und führen Sie dann Abfragen aus, um sich mit der neuen Struktur vertraut zu machen.

> [!div class="nextstepaction"]
> [Schnellstart: Portal-Assistent für Import, Indizierung und Abfragen](search-get-started-portal.md)