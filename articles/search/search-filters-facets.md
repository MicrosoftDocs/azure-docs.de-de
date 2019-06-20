---
title: Facettenfilter für die Suchnavigation in Apps – Azure Search
description: Filtern Sie nach Kriterien wie Benutzersicherheits-ID, geografischem Standort oder numerischen Werten, um Suchergebnisse bei Abfragen in Azure Search (in Microsoft Azure gehosteter Cloudsuchdienst) einzugrenzen.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8dffc5b87aefe23953d3a74f1d96b5ee03e0315d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597394"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Erstellen eines Facettenfilters in Azure Search 

Die Facettennavigation wird zum selbstgesteuerten Filtern von Abfrageergebnissen in einer Such-App verwendet, in der Ihre Anwendung Benutzeroberflächen-Steuerelemente zum Begrenzen der Suche auf bestimmte Gruppen von Dokumenten enthält (z.B. Kategorien oder Marken). Azure Search stellt hierfür die zugrunde liegende Datenstruktur bereit. Dieser Artikel enthält eine schnelle Einführung in die grundlegenden Schritte zum Erstellen einer Struktur für die Facettennavigation, die als Grundlage für die bereitzustellende Suchbenutzeroberfläche dient. 

> [!div class="checklist"]
> * Auswählen von Feldern zum Filtern und Durchführen von Faceting
> * Festlegen von Attributen für das Feld
> * Erstellen des Index und Laden von Daten
> * Hinzufügen von Facettenfiltern zu einer Abfrage
> * Verarbeiten von Ergebnissen

Facets sind dynamisch und werden für eine Abfrage zurückgegeben. Suchantworten verfügen über die Facetkategorien, die zum Navigieren durch die Ergebnisse verwendet werden. Wenn Sie mit Facets nicht vertraut sind, hilft Ihnen die Darstellung einer Struktur für die Facetnavigation im folgenden Beispiel weiter.

  ![](./media/search-filters-facets/facet-nav.png)

Benötigen Sie ausführlichere Informationen zur Facetnavigation? Sie finden diese Informationen unter [Implementieren der Facettennavigation in Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Auswählen von Feldern

Facets können über Felder mit einzelnen Werten und auch über Sammlungen berechnet werden. Felder, die für die Facettennavigation am besten funktionieren, weisen eine niedrige Kardinalität auf: eine geringe Anzahl von unterschiedlichen Werten, die sich in den Dokumenten Ihres Suchkorpus wiederholen (z. B. eine Liste mit Farben, Ländern/Regionen oder Markennamen). 

Das Faceting wird pro Feld aktiviert, wenn Sie den Index erstellen, indem Sie das `facetable`-Attribut auf `true` festlegen. Generell sollten Sie das `filterable`-Attribut auch für solche Felder auf `true` festlegen, damit Ihre Suchanwendung nach diesen Feldern auf Grundlage der Facetten, die der Endbenutzer auswählt, filtern kann. 

Bei der Erstellung eines Index mithilfe der REST-API, wird jeder [Feldtyp](https://docs.microsoft.com/rest/api/searchservice/supported-data-types), der theoretisch für die Facettennavigation verwendet werden könnte, standardmäßig als `facetable` markiert:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numerische Feldtypen: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ Sammlungen der obigen Typen (z. B. `Collection(Edm.String)` oder `Collection(Edm.Double)`)

`Edm.GeographyPoint`- oder `Collection(Edm.GeographyPoint)`-Felder eignen sich nicht für die Facettennavigation. Facetten funktionieren am besten mit Feldern mit einer geringen Kardinalität. Aufgrund der Auflösung von Geokoordinaten kommt es nur selten vor, dass zwei beliebige Gruppen von Koordinaten in einem gegebenen Dataset identisch sind. Daher werden Facets für Geokoordinaten nicht unterstützt. Sie benötigen ein Feld für einen Ort oder eine Region, um das Facettieren nach dem Standort durchzuführen.

## <a name="set-attributes"></a>Festlegen von Attributen

Indexattribute, mit denen gesteuert wird, wie ein Feld verwendet wird, werden einzelnen Felddefinitionen im Index hinzugefügt. Im folgenden Beispiel bestehen Felder mit geringer Kardinalität, die für das Faceting nützlich sind, aus den folgenden Komponenten: `category` (hotel, motel, hostel), `tags` und `rating`. Für diese Felder wurden die `filterable`- und `facetable`-Attribute im folgenden Beispiel zu Demonstrationszwecken explizit festgelegt. 

> [!Tip]
> Die bewährte Methode zur Leistungs- und Speicheroptimierung besteht darin, das Faceting für Felder zu deaktivieren, die niemals als Facet genutzt werden sollten. Insbesondere Zeichenfolgenfelder für eindeutige Werte, z. B. eine ID oder ein Produktname, sollten auf `"facetable": false` festgelegt werden, damit sie nicht versehentlich (und ineffektiv) für die Facettennavigation verwendet werden.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Diese Indexdefinition wurde aus [Erstellen eines Azure Search-Indexes mit der REST-API](https://docs.microsoft.com/azure/search/search-create-index-rest-api) kopiert. Sie ist bis auf einige vernachlässigbare Unterschiede bei den Felddefinitionen identisch. Die `filterable`- und `facetable`-Attribute werden `category`-, `tags`-, `parkingIncluded`-, `smokingAllowed`- und `rating`-Feldern explizit hinzugefügt. In der Praxis wären `filterable` und `facetable` standardmäßig für diese Felder aktiviert, wenn die REST-API verwendet wird. Wenn das .NET SDK verwendet wird, müssen diese Attribute explizit aktiviert werden.

## <a name="build-and-load-an-index"></a>Erstellen und Laden eines Index

Ein (wahrscheinlich naheliegender) Zwischenschritt vor der Erstellung einer Abfrage ist das [Erstellen und Auffüllen des Index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index). Er wird daher nur der Vollständigkeit halber erwähnt. Die Verfügbarkeit des Index kann unter anderem durch das Überprüfen der Indexliste im [Portal](https://portal.azure.com) ermittelt werden.

## <a name="add-facet-filters-to-a-query"></a>Hinzufügen von Facettenfiltern zu einer Abfrage

Erstellen Sie im Anwendungscode eine Abfrage, mit der alle Teile einer gültigen Abfrage angegeben werden, z.B. Suchausdrücke, Facets, Filter, Bewertungsprofile – alle Elemente, die zum Formulieren einer Anforderung verwendet werden. Im folgenden Beispiel wird eine Anforderung erstellt, bei der die Facettennavigation basierend auf der Art der Unterbringung, der Bewertung und anderen Annehmlichkeiten erstellt wird.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Zurückgeben von gefilterten Ergebnissen bei Klickereignissen

Wenn der Endbenutzer auf einen Facettenwert klickt, sollte der Handler für das Klickereignis einen Filterausdruck verwenden, um die Absicht des Benutzer umsetzen zu können. Beim Facet `category` wird das Klicken auf die Kategorie „motel“ beispielweise über einen `$filter`-Ausdruck implementiert, mit dem Unterbringungsmöglichkeiten dieser Art ausgewählt werden. Wenn ein Benutzer auf „motel“ klickt, um anzugeben, dass nur Motels angezeigt werden sollen, enthält die nächste von der Anwendung gesendete Abfrage `$filter=category eq 'motel'`.

Mit dem folgenden Codeausschnitt wird die Kategorie dem Filter hinzugefügt, wenn ein Benutzer einen Wert aus dem Facet „category“ auswählt.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Wenn der Benutzer auf ein Facettenwert für ein Sammlungsfeld wie `tags` klickt, z. B. auf den Wert „pool“, sollte Ihre Anwendung die folgende Filtersyntax verwenden: `$filter=tags/any(t: t eq 'pool')`.

## <a name="tips-and-workarounds"></a>Tipps und Problemumgehungen

### <a name="initialize-a-page-with-facets-in-place"></a>Initialisieren einer Seite mit vorhandenen Facets

Wenn Sie eine Seite mit vorhandenen Facets initialisieren möchten, können Sie eine Abfrage als Teil der Seiteninitialisierung senden, um für die Seite das Seeding mit einer Facet-Anfangsstruktur durchzuführen.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Beibehalten einer Struktur für die Facetnavigation asynchron zu gefilterten Ergebnissen

Eine der Herausforderungen bei der Facetnavigation in Azure Search besteht darin, dass Facets nur für aktuelle Ergebnisse vorhanden sind. In der Praxis ist die Beibehaltung einer statischen Gruppe von Facets üblich, damit Benutzer die Navigation in umgekehrter Richtung durchführen und die Schritte nachvollziehen können, um andere Wege durch die Suchinhalte zu erkunden. 

Dies ist zwar ein häufiger Anwendungsfall, aber dieser Vorgang ist noch nicht standardmäßig im Lieferumfang der Struktur für die Facettennavigation enthalten. Entwickler, die statische Facets benötigen, umgehen diese Einschränkung normalerweise, indem sie zwei gefilterte Abfragen durchführen: eine für die Ergebnisse und die andere zum Erstellen einer statischen Liste mit Facets zu Navigationszwecken.

## <a name="see-also"></a>Weitere Informationen

+ [Filter in Azure Search](search-filters.md)
+ [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Create Index (Azure Search-Dienst-REST-API))
+ [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API))
