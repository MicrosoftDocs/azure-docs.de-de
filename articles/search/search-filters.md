---
title: Filtern nach Suchergebnissen
titleSuffix: Azure Cognitive Search
description: Filtern Sie nach Benutzersicherheits-ID, Sprache, geografischem Standort oder numerischen Werten, um Suchergebnisse bei Abfragen in der kognitiven Azure-Suche, einem in Microsoft Azure gehosteten Cloudsuchdienst, zu reduzieren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d83e5c39f97db49e2cc9b77cc806cff0a1fa6de
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355983"
---
# <a name="filters-in-azure-cognitive-search"></a>Filter in der kognitiven Azure-Suche 

Ein *Filter* liefert Kriterien für die Auswahl von Dokumenten, die in einer Abfrage der kognitiven Azure-Suche verwendet werden. Bei einer ungefilterten Suche werden alle Dokumente im Index durchsucht. Ein Filter begrenzt eine Suchabfrage auf eine Teilmenge von Dokumenten. Beispielsweise kann ein Filter die Volltextsuche auf Produkte einer bestimmten Marke oder mit einer Farbe beschränken, wenn der Preis über einer bestimmten Schwelle liegt.

Einige Suchumgebungen setzen Filterbedingungen im Rahmen der Implementierung durch. Sie können jedoch jederzeit Filter verwenden, wenn Sie die Suche mithilfe *wertbasierter* Kriterien einschränken möchten (z. B. die Suche nach dem Produkttyp "Bücher" auf die Kategorie "Sachbuch" von "Kiepenheuer & Witsch" begrenzen).

Wenn Sie stattdessen gezielt nach bestimmten *Datenstrukturen* suchen möchten (d.h. die Suche auf das Feld "Kundenrezensionen" begrenzen), gibt es alternative Methoden, die nachfolgend beschrieben werden.

## <a name="when-to-use-a-filter"></a>Einsatzmöglichkeiten von Filtern

Filter sind die Grundlage mehrerer Suchoberflächen, darunter „In meiner Nähe finden“, Facettennavigation und Sicherheitsfilter, die nur die Dokumente anzeigen, die ein Benutzer sehen darf. Wenn Sie eine dieser Oberflächen implementieren, ist ein Filter erforderlich. Es ist der an die Suchabfrage angefügte Filter, der die Geolocationkoordinaten, die vom Benutzer ausgewählte Facettenkategorie oder die Sicherheits-ID des Anforderers angibt.

Es folgen Beispielszenarien:

1. Verwenden Sie einen Filter, um Ihren Index anhand von Datenwerten im Index zu segmentieren. Anhand eines Schemas mit Stadt, Gebäudetyp und Ausstattung können Sie einen Filter erstellen, um Dokumente explizit auszuwählen, die Ihren Kriterien entsprechen (in Seattle, Eigentumswohnungen, am Wasser). 

   Eine Volltextsuche mit den gleichen Eingaben führt oft zu ähnlichen Ergebnissen, doch ein Filter ist insofern präziser, als er eine exakte Übereinstimmung des Filterbegriffs mit dem Inhalt in Ihrem Index fordert. 

2. Verwenden Sie einen Filter, wenn die Suchoberfläche eine Filteranforderung aufweist:

   * [Facettennavigation](search-faceted-navigation.md) verwendet einen Filter, um die vom Benutzer gewählte Facettenkategorie zurückzugeben.
   * Die geografische Suche verwendet einen Filter, um Koordinaten des aktuellen Standorts an Apps des Typs „In meiner Nähe finden“ zu übergeben. 
   * Sicherheitsfilter übergeben Sicherheitsbezeichner als Filterkriterien, wobei eine Übereinstimmung im Index als Proxy für Zugriffsrechte für das Dokument dient.

3. Verwenden Sie einen Filter, wenn Suchkriterien für ein numerisches Feld gewünscht sind. 

   Numerische Felder sind im Dokument abrufbar und können in Suchergebnissen angezeigt werden, sind aber (bei einer Volltextsuche) nicht einzeln durchsuchbar. Wenn Sie Auswahlkriterien basierend auf numerischen Daten benötigen, verwenden Sie einen Filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternative Methoden zum Verkleinern des Suchbereichs

Wenn Sie Ihre Suchergebnissen eingrenzen möchten, sind Filter nicht die einzige Möglichkeit. Je nach Ziel können diese Alternativen besser geeignet sein:

 + `searchFields`-Abfrageparameter binden die Suche an bestimmte Felder. Wenn Ihr Index beispielsweise getrennte Felder für englische und spanische Beschreibungen enthält, können Sie mithilfe von „searchFields“ festlegen, welche Felder für die Volltextsuche verwendet werden sollen. 

+ Der Parameter `$select` dient zur Angabe, welche Felder in ein Resultset aufgenommen werden sollen, wobei die Antwort vor dem Senden an die aufrufende Anwendung effektiv angepasst wird. Durch diesen Parameter wird weder die Abfrage optimiert noch die Dokumentsammlung verkleinert, aber wenn eine kleinere Antwort Ihr Ziel ist, sollten Sie diesen Parameter als Option in Betracht ziehen. 

Weitere Informationen zu beiden Parametern finden Sie unter [Dokumente durchsuchen > Anforderung > Abfrageparameter](/rest/api/searchservice/search-documents#query-parameters).


## <a name="how-filters-are-executed"></a>Ausführen von Filtern

Zur Abfragezeit akzeptiert ein Filterparser Kriterien als Eingabe, wandelt den Ausdruck in unteilbare boolesche Ausdrücke um, die als Struktur dargestellt werden, und wertet dann die Filterstruktur über filterbare Felder in einem Index aus.

Die Filterung erfolgt zusammen mit Suche und bestimmt, welche Dokumente für den Dokumentabruf und die Relevanzbewertung in die nachgelagerte Verarbeitung einbezogen werden sollen. In Kombination mit einem Suchbegriff reduziert der Filter wirkungsvoll die Abrufmenge des nachfolgenden Suchvorgangs. Bei alleiniger Verwendung (z. B. wenn die Abfragezeichenfolge bei `search=*` leer ist) ist das Filterkriterium die einzige Eingabe. 

## <a name="defining-filters"></a>Definieren von Filtern
Filter sind OData-Ausdrücke, die mittels einer [in der kognitiven Azure-Suche unterstützten Teilmenge der OData V4-Syntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search) definiert werden. 

Sie können für jeden **Suchvorgang** einen Filter angeben. Doch der Filter selbst kann mehrere Felder, mehrere Kriterien und, wenn Sie eine **ismatch**-Funktion verwenden, mehrere Volltextsuchausdrücke enthalten. Bei einem mehrteiligen Filterausdruck können Sie Prädikate in beliebiger Reihenfolge angeben (gemäß den Regeln der Rangfolge von Operatoren). Es ergibt sich kein nennenswerter Leistungszuwachs, wenn Sie versuchen, Prädikate in einer bestimmten Reihenfolge neu anzuordnen.

Einer der Grenzwerte für einen Filterausdruck ist die maximale Größe der Anforderung. Die gesamte Anforderung kann einschließlich Filter eine maximale Größe von 16 MB für POST- bzw. 8 KB für GET-Vorgänge haben. Die Anzahl der Klauseln in Ihrem Filterausdruck ist ebenfalls begrenzt. Gut merken lässt sich, dass Sie bei Hunderten von Klauseln Gefahr laufen, an den Grenzwert zu erreichen. Wir empfehlen Ihnen, Ihre Anwendung so zu entwerfen, dass sie keine Filter unbegrenzter Größe erzeugt.

Die folgenden Beispiele veranschaulichen prototypische Filterdefinitionen in mehrere APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtern von Verwendungsmustern

Die folgenden Beispiele veranschaulichen einige Verwendungsmuster für Filterszenarios. Weitere Vorschläge finden Sie unter [OData-Ausdruckssyntax > Beispiele](./search-query-odata-filter.md#examples).

+ Eigenständige **$filter**-Filter ohne Abfragezeichenfolge; nützlich, wenn der Filterausdruck Dokumente von Interesse vollständig qualifizieren kann. Ohne Abfragezeichenfolge gibt es keine lexikalische oder linguistische Analyse, Bewertung und Rangfolge. Beachten Sie, dass die Suchzeichenfolge nur ein Sternchen ist, und alle Dokumente abgeglichen werden.

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Kombination aus Abfragezeichenfolge und **$filter**, wobei der Filter die Teilmenge erstellt und die Abfragezeichenfolge die Begriffseingaben für die Volltextsuche über die gefilterte Teilmenge liefert. Das Hinzufügen von Begriffen („Theater in Gehweite“) führt zu Suchbewertungen in den Ergebnissen, bei denen Dokumente, die am besten zu den Begriffen passen, höher eingestuft werden. Das Verwenden eines Filters mit einer Abfragezeichenfolge ist das verbreitetste Verwendungsmuster.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Zusammengesetzte Abfragen, getrennt durch "or", jede mit eigenen Filterkriterien (z.B. 'beagles' in' dog' oder' siamese' in' cat'). Ausdrücke in Kombination mit `or` werden einzeln ausgewertet. Dabei stimmt die Vereinigung der Dokumente mit jedem Ausdruck überein, der in der Antwort zurückgesendet wird. Dieses Nutzungsmuster erfolgt über die `search.ismatchscoring`-Funktion. Sie können auch die nicht bewertende Version (`search.ismatch`) verwenden.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Es ist auch möglich, die Volltextsuche über `search.ismatchscoring` mit den Filtern `and` anstelle von `or` zu kombinieren. Diese Funktion entspricht der Suche mit den Parametern `search` und `$filter` in einer Suchanforderung. Die folgenden beiden Abfragen erzielen beispielsweise das gleiche Ergebnis:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Diese Artikel bieten eine umfassende Anleitung zu bestimmten Anwendungsfällen:

+ [Facettenfilter](search-filters-facets.md)
+ [Sprachfilter](search-filters-language.md)
+ [Einschränkung aus Sicherheitsgründen](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Feldanforderungen für das Filtern

In der REST-API ist „filterable“ (filterbar) für einfache Felder standardmäßig *aktiviert*. Filterbare Felder erhöhen die Indexgröße. Stellen Sie sicher, dass Sie `"filterable": false` für Felder festlegen, die Sie nicht in einem Filter verwenden möchten. Weitere Informationen zu Einstellungen für Felddefinitionen finden Sie unter [Erstellen eines Indexes](/rest/api/searchservice/create-index).

Im .NET SDK ist die Eigenschaft „filterable“ standardmäßig *deaktiviert*. Sie können Felder filterbar machen, indem Sie die [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)-Eigenschaft des entsprechenden [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield)-Objekts auf `true` festlegen. Im folgenden Beispiel ist das Attribut auf die `BaseRate`-Eigenschaft einer Modellklasse festgelegt, die der Indexdefinition zugeordnet wird.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Aktivieren der Filterbarkeit für vorhandene Felder

Sie können vorhandene Felder nicht so ändern, dass sie filterbar sind. Sie müssen stattdessen ein neues Feld hinzufügen oder den Index neu erstellen. Weitere Informationen zur Neuerstellung eines Index oder der Wiederauffüllung von Feldern finden Sie unter [Neuerstellen eines Index für die kognitive Azure-Suche](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Grundlegendes zu Textfiltern

Textfilter entsprechen Zeichenfolgefeldern mit Literalzeichenfolgen, die Sie im Filter angeben. Im Gegensatz zur Volltextsuche erfolgt bei Textfiltern keine lexikalische Analyse oder Worttrennung, sodass Vergleiche nur auf exakte Übereinstimmungen abzielen. Nehmen wir beispielsweise an, dass das Feld *f* „sunny day“ enthält. Dann stimmt zwar `$filter=f eq 'Sunny'` nicht überein, `$filter=f eq 'sunny day'` allerdings schon. 

Bei Textzeichenfolgen wird Groß-/Kleinschreibung berücksichtigt. Es gibt keine Kleinschreibung großgeschriebener Wörter. `$filter=f eq 'Sunny day'` findet deshalb „sunny day“ nicht.

### <a name="approaches-for-filtering-on-text"></a>Ansätze zum Filtern von Text

| Vorgehensweise | BESCHREIBUNG | Verwendung |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Eine Funktion, bei der ein Feld mit einer durch Trennzeichen getrennte Liste von Zeichenfolgen entspricht. | Dies wird für [Sicherheitsfilter](search-security-trimming-for-azure-search.md) und alle anderen Filter empfohlen, in denen viele unformatierte Textwerte mit einem Zeichenfolgenfeld verglichen werden. Die Funktion **search.in** ist auf Geschwindigkeit ausgelegt und viel schneller als das explizite Vergleichen des Felds mit jeder Zeichenfolge mithilfe von `eq` und `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Eine Funktion, die es erlaubt, Volltextsuchvorgänge mit strikt booleschen Filteroperationen im selben Filterausdruck zu kombinieren. | Verwenden Sie **search.ismatch** (oder das Äquivalent **search.ismatchscoring**), wenn Sie in einer Anforderung mehrere Suchfilterkombinationen möchten. Sie können sie auch für den Filter *enthält* verwenden, um auf eine Teilzeichenfolge innerhalb einer größeren Zeichenfolge zu filtern. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Ein benutzerdefinierter Ausdruck bestehend aus Feldern, Operatoren und Werten. | Verwenden Sie diesen Ausdruck, wenn Sie genaue Übereinstimmungen zwischen Zeichenfolgefeldern und einem Zeichenfolgenwert finden möchten. |

## <a name="numeric-filter-fundamentals"></a>Grundlegendes zu numerischen Filtern

Numerische Felder sind im Kontext der Volltextsuche nicht `searchable`. Nur Zeichenfolgen werden einer Volltextsuche unterzogen. Wenn Sie z.B. 99,99 als Suchbegriff eingeben, werden keine Artikel mit dem Preis 99,99 USD zurückgegeben. Stattdessen sehen Sie Elemente, die den Wert 99 in Zeichenfolgenfeldern des Dokuments enthalten. Wenn Sie also numerische Daten haben, gilt die Annahme, dass Sie sie für Filter verwenden, einschließlich Bereiche, Facetten, Gruppen usw. 

Dokumente, die numerische Felder enthalten (Preis, Größe, SKU, ID), geben diese Werte in Suchergebnissen zurück, wenn das Feld mit `retrievable` markiert ist. Der Punkt hier ist, dass die Volltextsuche selbst nicht auf numerische Feldtypen anwendbar ist.

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie als Erstes den **Suchexplorer** im Portal aus, um Abfragen mit **$filter**-Parametern zu übermitteln. Der [Index von „real-estate-sample“](search-get-started-portal.md) liefert interessante Ergebnisse für die folgenden gefilterten Abfragen, wenn Sie diese in die Suchleiste einfügen:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Weitere Beispiele finden Sie unter [OData-Filterausdrucksyntax > Beispiele](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Weitere Informationen

+ [Funktionsweise der Volltextsuche in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Search Documents (Azure Search Service REST API)](/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API))
+ [Einfache Abfragesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-Abfragesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Unterstützte Datentypen](/rest/api/searchservice/supported-data-types)