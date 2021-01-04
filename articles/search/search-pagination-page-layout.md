---
title: Arbeiten mit Suchergebnissen
titleSuffix: Azure Cognitive Search
description: Strukturieren und Sortieren von Suchergebnissen, Abrufen der Dokumentanzahl und Hinzufügen einer Inhaltsnavigation zu den Suchergebnissen in der kognitiven Azure-Suche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e583cedc04113615c50cc9906cbd11a99ff48683
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421718"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Arbeiten mit Suchergebnissen in der kognitiven Azure-Suche

In diesem Artikel wird erläutert, wie Sie eine Abfrageantwort abrufen können, die eine Gesamtanzahl übereinstimmender Dokumente, paginierter Ergebnisse, sortierter Ergebnisse sowie von Begriffen mit markierten Treffern enthält.

Die Struktur einer Antwort wird durch Parameter in der Abfrage bestimmt: [Suchdokument](/rest/api/searchservice/Search-Documents) in der REST-API oder [SearchResults-Klasse](/dotnet/api/azure.search.documents.models.searchresults-1) im .NET SDK.

## <a name="result-composition"></a>Ergebniszusammensetzung

Ein Suchdokument kann zwar aus einer Vielzahl von Feldern bestehen. In der Regel werden jedoch nur wenige zur Darstellung der einzelnen Dokumente im Resultset benötigt. Fügen Sie bei einer Abfrageanforderung `$select=<field list>` an, um anzugeben, welche Felder in der Antwort angezeigt werden. Ein Feld muss im Index durch das Attribut **retrievable** gekennzeichnet sein, damit es in ein Ergebnis einbezogen wird. 

Besonders gut eignen sich Felder, die Dokumente vergleichen und zwischen Dokumenten unterscheiden und so genügend Informationen liefern, um den Benutzer zu einer Antwort durch Klicken einzuladen. Auf einer E-Commerce-Website kann dies ein Produktname, eine Beschreibung, eine Marke, eine Farbe, eine Größe, ein Preis oder eine Bewertung sein. Bei dem integrierten Beispiel „hotels-sample-index“ können dies die Felder im folgenden Beispiel sein:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Wenn Sie in ein Ergebnis Bilddateien wie etwa ein Produktfoto oder ein Logo einbeziehen möchten, speichern Sie diese außerhalb von Azure Cognitive Search. Fügen Sie jedoch in Ihren Index ein Feld ein, das auf die Bild-URL im Suchdokument verweist. Beispielindizes, die in Ergebnissen Bilder unterstützen, enthalten die Demo **realestate-sample-us**, die in dieser [Schnellstartanleitung](search-create-app-portal.md) vorgestellt wird, sowie die Demo-App [New York City Jobs](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Auslagerungsergebnisse

Das Suchmodul gibt standardmäßig die durch die Suchbewertung bestimmten ersten 50 Treffer zurück, wenn es sich bei der Abfrage um eine Volltextsuche handelt. Bei Abfragen mit dem Kriterium der genauen Übereinstimmung werden die Treffer in beliebiger Reihenfolge zurückgegeben.

Wenn eine andere Anzahl an übereinstimmenden Dokumenten zurückgegeben werden soll, fügen Sie der Abfrageanforderung die Parameter `$top` und `$skip` hinzu. In der folgenden Liste wird die Logik erläutert.

+ Fügen Sie `$count=true` hinzu, um die Gesamtzahl von übereinstimmenden Dokumenten abzurufen.

+ Damit wird die erste Gruppe von 15 übereinstimmenden Dokumenten sowie die Gesamtzahl der Übereinstimmungen zurückgegeben: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Damit wird die zweite Gruppe zurückgegeben, wobei die ersten 15 übersprungen und die nächsten 15 abgerufen werden: `$top=15&$skip=15`. Damit geschieht dasselbe für die dritte Gruppe von 15 Dokumenten: `$top=15&$skip=30`

Es ist nicht sicher, dass die Ergebnisse von paginierten Abfragen stabil sind, wenn sich der zugrunde liegende Index ändert. Beim Paging ändert sich der Wert von `$skip` für die einzelnen Seiten. Dabei sind die einzelnen Abfragen jedoch unabhängig und werden für die aktuelle Ansicht der Daten ausgeführt, die im Index zur Abfragezeit vorhanden sind. (Das bedeutet, es gibt kein Zwischenspeichern bzw. keine Momentaufnahmen der Ergebnisse wie etwa bei universellen Datenbanken.)
 
Mit dem folgenden Beispiel werden möglicherweise Duplikate zurückgegeben. Stellen Sie sich einen Index mit vier Dokumenten vor:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Stellen Sie sich nun vor, es sollen immer je zwei Ergebnisse nach Bewertung sortiert zurückgegeben werden. Sie würden die folgende Abfrage ausführen, um die erste Seite mit Ergebnissen abzurufen: `$top=2&$skip=0&$orderby=rating desc`. Damit werden die folgenden Ergebnisse erzeugt:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
Stellen Sie sich nun vor, dass bei dem Dienst zwischen den Abfragen `{ "id": "5", "rating": 4 }` als fünftes Dokument in den Index aufgenommen wird.  Kurze Zeit später führen Sie eine Abfrage aus, um die zweite Seite abzurufen: `$top=2&$skip=2&$orderby=rating desc`. Damit werden die folgenden Ergebnisse abgerufen:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Wie Sie sehen, wurde Dokument 2 zweimal abgerufen. Der Grund dafür ist, dass das neue Dokument 5 einen größeren Wert für die Bewertung aufweist, sodass es in der Reihenfolge vor Dokument 2 kommt und daher auf der ersten Seite landet. Dieses Verhalten ist zwar möglicherweise unerwartet, jedoch typisch für das Verhalten eines Suchmoduls.

## <a name="ordering-results"></a>Sortieren von Ergebnissen

Bei Volltextsuchabfragen werden Ergebnisse automatisch nach einer Suchbewertung geordnet, die basierend auf Begriffshäufigkeit und -nähe berechnet wird, wobei Dokumente mit mehr oder stärkeren Übereinstimmungen bei einem Suchbegriff höher bewertet werden. 

Suchergebnisse vermitteln einen allgemeinen Eindruck von Relevanz, der die Stärke der Übereinstimmung im Vergleich zu anderen Dokumenten im selben Resultset widerspiegelt. Bewertungen sind von einer Abfrage zur nächsten nicht immer konsistent, sodass Sie möglicherweise geringfügige Abweichungen in der Reihenfolge der Suchdokumente feststellen werden. Hierfür kann es verschiedene Ursachen geben.

| Ursache | BESCHREIBUNG |
|-----------|-------------|
| Datenflüchtigkeit | Der Indexinhalt variiert, wenn Sie Dokumente hinzufügen, ändern oder löschen. Begriffshäufigkeiten ändern sich, wenn im Laufe der Zeit Indexaktualisierungen verarbeitet werden, die sich auf die Suchbewertungen für übereinstimmende Dokumente auswirken. |
| Mehrere Replikate | Bei Diensten, die mehrere Replikate verwenden, werden Abfragen parallel für jedes Replikat ausgegeben. Die zum Berechnen einer Suchbewertung verwendete Indexstatistik wird pro Replikat berechnet, wobei die Ergebnisse zusammengeführt und in der Abfrageantwort sortiert werden. Replikate sind größtenteils Spiegelungen. Statistiken können sich jedoch aufgrund geringfügiger Zustandsabweichungen voneinander unterscheiden. So könnten beispielsweise in einem Replikat Dokumente gelöscht worden sein, die aus anderen Replikaten zusammengeführt wurden, was zur jeweiligen Statistik beiträgt. Unterschiede in Statistiken, die pro Replikat berechnet werden, fallen in kleineren Indizes deutlicher auf. |
| Identische Bewertungen | Wenn mehrere Dokumente dieselbe Bewertung aufweisen, wird eine davon zuerst angezeigt.  |

### <a name="consistent-ordering"></a>Konsistente Reihenfolge

Angesichts der flexiblen Ergebnissortierung sollten Sie andere Optionen in Erwägung ziehen, wenn Konsistenz eine Anwendungsanforderung ist. Der einfachste Ansatz ist die Sortierung nach einem Feldwert wie etwa nach Bewertung oder nach Datum. In Szenarios, in denen nach einem bestimmten Feld wie etwa Bewertung oder Datum sortiert werden soll, können Sie einen [`$orderby`-Ausdruck](query-odata-filter-orderby-syntax.md) definieren, der auf alle als **sortierbar** indizierten Felder angewendet werden kann.

Eine weitere Möglichkeit ist die Verwendung eines [benutzerdefinierten Bewertungsprofils](index-add-scoring-profiles.md). Mit Bewertungsprofilen haben Sie mehr Kontrolle über die Bewertung von Elementen in Suchergebnissen und können in bestimmten Feldern gefundene Übereinstimmungen aufwerten. Mithilfe der zusätzlichen Bewertungslogik können geringfügige Unterschiede zwischen Replikaten außer Kraft gesetzt werden, da die Suchbewertungen für die einzelnen Dokumente weiter auseinander liegen. Für diesen Ansatz wird der [Rangfolgenalgorithmus](index-ranking-similarity.md) empfohlen.

## <a name="hit-highlighting"></a>Treffermarkierung

Die Treffermarkierung bezieht sich auf die Textformatierung (wie Fett- oder Gelbmarkierung), die auf Treffer in einem Ergebnis angewendet werden, sodass die Übereinstimmungen leicht zu erkennen sind. Anweisungen zur Treffermarkierung werden in der [Abfrageanforderung](/rest/api/searchservice/search-documents) bereitgestellt. 

Um die Treffermarkierung zu aktivieren, fügen Sie `highlight=[comma-delimited list of string fields]` hinzu, um anzugeben, für welche Felder die Markierung verwendet wird. Die Markierung eignet sich für längere Inhaltsfelder, z. B. ein Beschreibungsfeld, bei denen die Übereinstimmung nicht sofort ersichtlich ist. Für die Treffermarkierung eignen sich nur Felddefinitionen, die das Attribut **durchsuchbar** aufweisen.

Azure Cognitive Search gibt standardmäßig bis zu fünf Markierungen pro Feld zurück. Sie können die Anzahl anpassen, indem Sie einen Bindestrich gefolgt von einer ganzen Zahl an das Feld anfügen. `highlight=Description-10` gibt beispielsweise bis zu 10 Markierungen zu übereinstimmenden Inhalten im Beschreibungsfeld zurück.

Die Formatierung wird auf Abfragen kompletter Begriffe angewendet. Der Formatierungstyp wird durch Tags (`highlightPreTag` und `highlightPostTag`) bestimmt, und die Antwort wird vom Code verarbeitet (z. B., wenn eine Schriftart fett formatiert oder ein gelber Hintergrund angewendet werden soll).

Im folgenden Beispiel sind die Begriffe „sandy“, „sand“, „beaches“ und „beach“ im Beschreibungsfeld zur Markierung mit Tags versehen. Bei Abfragen, die eine Abfrageerweiterung in der Engine auslösen, wie z. B. Fuzzy- und Platzhaltersuche, wird die Treffermarkierung nur begrenzt unterstützt.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Neues Verhalten (ab 15. Juli)

Dienste, die nach dem 15. Juli 2020 erstellt werden, verfügen über ein anderes Markierungsverhalten. Bei Diensten, die vor diesem Datum erstellt werden, bleibt das Markierungsverhalten unverändert. 

Für das neue Verhalten gilt:

* Es werden nur Ausdrücke zurückgegeben, die der vollständigen Ausdrucksabfrage entsprechen. Die Abfrage „super bowl“ führt zu Markierungen wie diesen:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Beachten Sie, dass für *bowl of chips* keine Markierung erfolgt, weil keine Entsprechung mit dem vollständigen Ausdruck vorliegt.

Berücksichtigen Sie diese Änderung, wenn Sie Clientcode mit einer Treffermarkierungsimplementierung schreiben. Beachten Sie, dass dies nur dann für Sie relevant ist, wenn Sie einen vollständig neuen Suchdienst erstellen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie schnell eine Suchseite für Ihren Client erstellen möchten, probieren Sie die folgenden Optionen aus:

+ Mit dem [Anwendungsgenerator](search-create-app-portal.md) im Portal können Sie eine HTML-Seite mit einer Suchleiste, einer Facettennavigation und einem Ergebnisbereich erstellen.
+ [Erstellen der ersten App in C#](tutorial-csharp-create-first-app.md) ist ein Tutorial, mit dem Sie einen funktionalen Client erstellen können. Der Beispielcode veranschaulicht paginierte Abfragen, Treffermarkierung und Sortierung.

Einige Codebeispiele beinhalten eine Web-Front-End-Schnittstelle, die Sie hier finden: [Jobs in New York City (Demo-App)](https://aka.ms/azjobsdemo), [JavaScript-Beispielcode mit einer Livedemo-Website](https://github.com/liamca/azure-search-javascript-samples) und [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).