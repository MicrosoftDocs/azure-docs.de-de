---
title: Abfragetypen und Komposition
titleSuffix: Azure Cognitive Search
description: Enthält die Grundlagen zur Erstellung einer Suchabfrage in der kognitiven Azure-Suche, indem Parameter zum Filtern, Auswählen und Sortieren von Ergebnissen verwendet werden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 362f46290bbe2008f9fb862a8711577050050192
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693248"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Abfragetypen und -komposition in der kognitiven Azure-Suche

In der kognitiven Azure-Suche ist eine Abfrage eine vollständige Spezifikation eines Roundtripvorgangs. Die Anforderung enthält Parameter, die Ausführungsanweisungen für die Engine enthalten, sowie Parameter zur Formatierung der zurückgegebenen Antwort. (`search=*`) ohne Angaben, ohne Übereinstimmungskriterien und ohne Parameter oder mit Standardparametern führt eine Abfrage für alle durchsuchbaren Felder als Volltextsuche aus, und es wird ein Resultset ohne Bewertungen in zufälliger Reihenfolge zurückgegeben.

Das folgende Beispiel ist eine repräsentative Abfrage, die in der [REST-API](/rest/api/searchservice/search-documents) erstellt wurde. Dieses Beispiel zielt auf den [Hotel-Demoindex](search-get-started-portal.md) ab und enthält allgemeine Parameter, um Ihnen eine Vorstellung vom Aussehen einer Abfrage zu vermitteln.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** legt den Parser fest. Es kann es sich dabei um den [einfachen Standardabfrageparser](search-query-simple-examples.md) (optimal für die Volltextsuche) handeln oder um den [vollständigen Lucene-Abfrageparser](search-query-lucene-examples.md), der für erweiterte Abfragekonstrukte wie reguläre Ausdrücke, NEAR-Suche, Fuzzy- und Platzhaltersuche usw. verwendet wird.

+ **`search`** gibt die Übereinstimmungskriterien an, normalerweise ganze Ausdrücke oder Formulierungen, häufig mit Booleschen Operatoren einhergehend. Einzelne eigenständige Begriffe sind *Begriffsabfragen*. In Anführungszeichen eingeschlossene mehrteilige Abfragen sind *Ausdrucksabfragen*. Die Suche kann undefiniert sein, wie in **`search=*`** , aber ohne Kriterien für die Übereinstimmung besteht das Resultset aus zufällig ausgewählten Dokumenten.

+ **`searchFields`** schränkt die Ausführung von Abfragen auf bestimmte Felder ein. Jedes im Indexschema als *durchsuchbar* (searchable) attributierte Feld ist ein Kandidat für diesen Parameter.

Antworten werden ebenfalls durch die Parameter bestimmt, die Sie in der Abfrage hinzufügen:

+ **`select`** gibt an, welche Felder in der Antwort zurückgegeben werden sollen. Nur die im Index als *abrufbar* (retrievable) markierten Felder können in einer SELECT-Anweisung verwendet werden.

+ **`top`** gibt die angegebene Anzahl der am besten übereinstimmenden Dokumente zurück. In diesem Beispiel werden nur 10 Treffer zurückgegeben. Sie können „Zurück zum Seitenanfang“ und „Überspringen“ verwenden (nicht dargestellt), um die Ergebnisse seitenweise auszugeben.

+ **`count`** gibt Aufschluss darüber, wie viele Dokumente im gesamten Index insgesamt übereinstimmen. Diese Zahl kann größer als die Zahl der zurückgegebenen Dokumente sein. 

+ **`orderby`** wird verwendet, wenn Sie die Ergebnisse nach einem Wert sortieren möchten, etwa nach einer Bewertung oder einem Standort. Andernfalls ist das Standardverhalten, das Relevanzergebnis zur Bewertung von Ergebnissen zu verwenden.

In der kognitiven Azure-Suche erfolgt die Abfrageausführung immer für einen Index, und für die Authentifizierung wird ein API-Schlüssel verwendet, der in der Anforderung angegeben ist. In REST wird beides in Anforderungsheadern angegeben.

### <a name="how-to-run-this-query"></a>Ausführen der Abfrage

Vor dem Schreiben von Code können Sie die Abfragetools verwenden, um die Syntax kennenzulernen und mit verschiedenen Parametern zu experimentieren. Der schnellste Ansatz ist das integrierte Portaltool, [Suchexplorer](search-explorer.md).

Wenn Sie die [Schnellstartanleitung zum Erstellen des Hotel-Demoindex](search-get-started-portal.md) ausgeführt haben, können Sie diese Abfragezeichenfolge in die Suchleiste des Explorers eingeben, um Ihre erste Abfrage auszuführen: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Ermöglichung von Abfragevorgängen durch den Index

Der Indexentwurf und der Abfrageentwurf sind in der kognitiven Azure-Suche eng aneinander gekoppelt. Wichtig zu wissen ist hierbei, dass das *Indexschema* mit Attributen für jedes Feld die Art der Abfrage bestimmt, die Sie erstellen können. 

Die Indexattribute für ein Feld bestimmen die zulässigen Vorgänge – ob ein Feld im Index *durchsuchbar* ist, ob es in Ergebnissen *abrufbar*, *sortierbar*, *filterbar* ist usw. In der Beispielabfragezeichenfolge funktioniert `"$orderby": "Rating"` nur, weil das Feld „Rating“ (Bewertung) im Indexschema als *sortierbar* (sortable) markiert ist. 

![Indexdefinition für das „hotels“-Beispiel](./media/search-query-overview/hotel-sample-index-definition.png "Indexdefinition für das „hotels“-Beispiel")

Der obige Screenshot zeigt eine unvollständige Liste der Indexattribute für das „hotels“-Beispiel. Das gesamte Indexschema können Sie im Portal anzeigen. Weitere Informationen zu Indexattributen finden Sie unter [Create Index REST API](/rest/api/searchservice/create-index) (REST-API zum Erstellen von Indizes).

> [!Note]
> Einige Abfragefunktionen werden für den gesamten Index und nicht nur für einzelne Felder aktiviert. Zu diesen Funktionen zählen [Synonymzuordnungen](search-synonyms.md), [benutzerdefinierte Analysetools](index-add-custom-analyzers.md), [Vorschlagskonstrukte (für AutoVervollständigen und vorgeschlagene Abfragen)](index-add-suggesters.md) sowie die [Bewertungslogik für Ergebnisse](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elemente einer Abfrageanforderung

Abfragen werden immer an einen einzelnen Index gerichtet. Es ist nicht möglich, Indizes zu verknüpfen oder benutzerdefinierte oder temporäre Datenstrukturen als Abfrageziel zu erstellen. 

Erforderliche Elemente in einer Abfrageanforderung enthalten die folgenden Komponenten:

+ Sammlung von Dienstendpunkten und Indexdokumenten, ausgedrückt als eine URL, die feste und benutzerdefinierte Komponenten enthält: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (nur REST) ist erforderlich, weil immer mehrere Versionen der API verfügbar sind. 
+ **`api-key`** (ein Abfrage- oder Administrator-API-Schlüssel) authentifiziert die Anforderung für den Dienst.
+ **`queryType`** (einfach oder vollständig) kann weggelassen werden, wenn Sie die integrierte einfache Standardsyntax verwenden.
+ **`search`** oder **`filter`** gibt die Übereinstimmungskriterien an, die weggelassen werden können, wenn Sie eine leere Suche durchführen möchten. Beide Abfragetypen werden in Bezug auf den einfachen Parser erläutert, doch auch erweiterte Abfragen erfordern den Parameter „search“ zur Übergabe komplexer Abfrageausdrücke.

Alle anderen Suchparameter sind optional. Die vollständige Liste der Attribute finden Sie unter [Create Index (REST)](/rest/api/searchservice/create-index) (Erstellen des Index [REST]). Eine ausführlichere Erläuterung, wie Parameter während der Verarbeitung verwendet werden, finden Sie unter [Funktionsweise der Volltextsuche in der kognitiven Azure-Suche](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Auswählen von APIs und Tools

In der folgenden Tabelle sind die APIs und toolbasierten Ansätze zum Übermitteln von Abfragen aufgeführt.

| Methodik | BESCHREIBUNG |
|-------------|-------------|
| [Suchexplorer (Portal)](search-explorer.md) | Stellt eine Suchleiste und Optionen für die Auswahl des Index und von API-Versionen bereit. Die Ergebnisse werden als JSON-Dokumente zurückgegeben. Empfohlen für das Erkunden, Testen und Validieren. <br/>[Weitere Informationen.](search-get-started-portal.md#query-index) | 
| [Postman oder andere REST-Tools](search-get-started-rest.md) | Webtesttools sind eine hervorragende Wahl für das Formulieren von REST-Aufrufen. Die REST-API unterstützt alle möglichen Vorgänge in der kognitiven Azure-Suche. In diesem Artikel erfahren Sie, wie Sie einen HTTP-Anforderungsheader und -text zum Senden von Anforderungen an die kognitive Azure-Suche einrichten.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Client, der zum Abfragen eines Index für die kognitive Azure-Suche verwendet werden kann.  <br/>[Weitere Informationen.](search-howto-dotnet-sdk.md)  |
| [Search Documents (REST API)](/rest/api/searchservice/search-documents) (Durchsuchen von Dokumenten (REST-API)) | GET- oder POST-Methoden für einen Index, wobei Abfrageparameter für zusätzliche Eingaben verwendet werden.  |

## <a name="choose-a-parser-simple--full"></a>Auswählen eines Parsers: einfach | vollständig

Die kognitive Azure-Suche basiert auf Apache Lucene und ermöglicht Ihnen die Wahl zwischen zwei Abfrageparsern zur Verarbeitung von typischen und spezialisierten Abfragen. Anforderungen mit dem einfachen Parser werden mit der [einfachen Abfragesyntax](query-simple-syntax.md) formuliert. Dieser Parser ist aufgrund seiner Geschwindigkeit und Effizienz bei Freitextabfragen standardmäßig ausgewählt. Diese Syntax unterstützt eine Reihe von allgemeinen Suchoperatoren, z.B. AND, OR, NOT, Begriff, Suffix und Rangfolgeoperatoren.

Die vollständige [Lucene-Abfragesyntax](query-Lucene-syntax.md#bkmk_syntax) wird aktiviert, wenn Sie der Anforderung `queryType=full` hinzufügen. So wird die häufig genutzte und ausdrucksstarke Abfragesprache verfügbar gemacht, die im Rahmen von [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) entwickelt wurde. Die vollständige Syntax erweitert die einfache Syntax. Jede Abfrage, die Sie für die einfache Syntax schreiben, kann mit dem vollständigen Lucene-Parser ausgeführt werden. 

Die folgenden Beispiele veranschaulichen den entscheidenden Punkt: Die gleiche Abfrage liefert bei der Verwendung unterschiedlicher queryType-Einstellungen verschiedene Ergebnisse. In der ersten Abfrage wird `^3` nach `historic` als Teil des Suchbegriffs behandelt. Das Ergebnis mit dem höchsten Rang für diese Abfrage lautet „Marquis Plaza & Suites“, dessen Beschreibung *ocean* enthält.

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Dieselbe Abfrage unter Verwendung des Lucene-Parsers interpretiert `^3` als Begriffsverstärker für den Feldinhalt. Durch das Wechseln des Parsers wird der Rang geändert, wobei Ergebnisse, die den Begriff *historic* enthalten, in den oberen Bereich verschoben werden.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Abfragetypen

Die kognitive Azure-Suche unterstützt eine breite Palette von Abfragetypen. 

| Abfragetyp | Verwendung | Beispiele und weitere Informationen |
|------------|--------|-------------------------------|
| Freitextsuche | Suchparameter und einer der beiden Parser| Die Volltextsuche sucht nach einem oder mehreren Begriffen in allen *durchsuchbaren* Feldern im Index. Sie funktioniert so, wie Sie es von einer Suchmaschine wie Google oder Bing erwarten. Bei dem Beispiel in der Einführung handelt es sich um eine Volltextsuche.<br/><br/>Die Volltextsuche durchläuft (standardmäßig) eine lexikalische Analyse mit dem Lucene-Standardanalysetool, um alle Begriffe in Kleinbuchstaben umzuwandeln und Stoppwörter wie „das“ zu entfernen. Sie können die Standardeinstellung außer Kraft setzen und durch [nicht englischsprachige Analysetools](index-add-language-analyzers.md#language-analyzer-list) oder [spezielle sprachunabhängig Analysetools](index-add-custom-analyzers.md#AnalyzerTable) überschreiben, die die lexikalische Analyse ändern. Ein Beispiel hierfür ist ein [Schlüsselwort](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html), durch das der gesamte Inhalt eines Felds als ein einzelnes Token behandelt wird. Dies ist nützlich für Daten wie Postleitzahlen, IDs und einige Produktnamen. | 
| Gefilterte Suche | [OData-Filterausdruck](query-odata-filter-orderby-syntax.md) und einer der beiden Parser | Filterabfragen werten einen booleschen Ausdruck für alle *filterbaren* Felder in einem Index aus. Im Gegensatz zu einer Suche wird bei einer Filterabfrage der genaue Inhalt eines Felds abgeglichen, einschließlich Unterscheidung nach Groß-/Kleinschreibung bei Zeichenfolgenfeldern. Ein weiterer Unterschied ist, dass Filterabfragen in der OData-Syntax ausgedrückt werden. <br/>[Beispiel für einen Filterausdruck](search-query-simple-examples.md#example-3-filter-queries) |
| Geografische Suche | [Typ „Edm.GeographyPoint“](/rest/api/searchservice/supported-data-types) für das Feld, Filterausdruck und einer der beiden Parser | In einem Feld mit dem Typ „Edm.GeographyPoint“ gespeicherte Koordinaten werden für Steuerelemente zur Umgebungssuche oder kartenbasierten Suche verwendet. <br/>[Beispiel für die geografische Suche](search-query-simple-examples.md#example-5-geo-search)|
| Bereichssuche | Filterausdruck und einfacher Parser | In der kognitiven Azure-Suche werden Bereichsabfragen anhand des Filterparameters erstellt. <br/>[Beispiel für den Bereichsfilter](search-query-simple-examples.md#example-4-range-filters) | 
| [Feldbezogene Suche](query-lucene-syntax.md#bkmk_fields) | Suchparameter und vollständiger Parser | Erstellen Sie einen zusammengesetzten Abfrageausdruck für ein einzelnes Feld. <br/>[Beispiel für die feldbezogene Suche](search-query-lucene-examples.md#example-2-fielded-search) |
| [Fuzzysuche](query-lucene-syntax.md#bkmk_fuzzy) | Suchparameter und vollständiger Parser | Sucht nach Begriffen mit ähnlichem Aufbau oder ähnlicher Rechtschreibung. <br/>[Beispiel für die Fuzzysuche](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [NEAR-Suche](query-lucene-syntax.md#bkmk_proximity) | Suchparameter und vollständiger Parser | Sucht nach Begriffen, die in einem Dokument nahe beieinander vorkommen. <br/>[Beispiel für die NEAR-Suche](search-query-lucene-examples.md#example-4-proximity-search) |
| [Term Boosting](query-lucene-syntax.md#bkmk_termboost) | Suchparameter und vollständiger Parser | Weist einem Dokument, das den verstärkten Begriff enthält, im Vergleich zu anderen Dokumenten, bei denen dies nicht der Fall ist, einen höheren Rang zu. <br/>[Beispiele für die Begriffsverstärkung (Term Boosting)](search-query-lucene-examples.md#example-5-term-boosting) |
| [Suche mit regulären Ausdrücken](query-lucene-syntax.md#bkmk_regex) | Suchparameter und vollständiger Parser | Sucht basierend auf dem Inhalt eines regulären Ausdrucks nach Übereinstimmungen. <br/>[Beispiel für einen regulären Ausdruck](search-query-lucene-examples.md#example-6-regex) |
|  [Platzhalter- oder Präfixsuche](query-lucene-syntax.md#bkmk_wildcard) | Suchparameter und vollständiger Parser | Sucht basierend auf einem Präfix und dem Tildezeichen (`~`) oder einem einzelnen Zeichen (`?`) nach Übereinstimmungen. <br/>[Beispiel für die Platzhaltersuche](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Verarbeiten von Suchergebnissen 

Abfrageergebnisse werden als JSON-Dokumente in der REST-API gestreamt, aber wenn Sie .NET-APIs verwenden, wird die Serialisierung integriert. Sie können Ergebnisse steuern, indem Sie Parameter für die Abfrage festlegen und bestimmte Felder für die Antwort auswählen.

Die Parameter der Abfrage können verwendet werden, um das Resultset wie folgt zu strukturieren:

+ Begrenzen der Anzahl von Dokumenten in den Ergebnissen (standardmäßig 50) bzw. Zusammenfassen zu Batches
+ Auswählen der Felder, die in den Ergebnissen enthalten sein sollen
+ Festlegen der Sortierreihenfolge
+ Hinzufügen von Treffermarkierungen, um übereinstimmende Begriffe im Text der Suchergebnisse hervorzuheben

### <a name="tips-for-unexpected-results"></a>Tipps zu unerwarteten Ergebnissen

Es kann vorkommen, dass der Inhalt und nicht die Struktur der Ergebnisse unerwartet ist. Wenn die Abfrageergebnisse nicht wie erwartet ausfallen, können Sie es mit diesen Änderungen der Abfragen versuchen, um zu ermitteln, ob sich die Ergebnisqualität verbessert:

+ Ändern Sie **`searchMode=any`** (Standard) in **`searchMode=all`** , damit Übereinstimmungen mit allen Kriterien erforderlich sind, nicht nur mit einem oder mehreren Kriterien. Dies gilt besonders, wenn die Abfrage boolesche Operatoren enthält.

+ Ändern Sie das Abfrageverfahren, wenn eine Textanalyse oder lexikalische Analyse erforderlich ist und der Abfragetyp eine linguistische Verarbeitung ausschließt. Bei der Volltextsuche werden im Rahmen der Textanalyse oder lexikalischen Analyse Rechtschreibfehler, Singular-/Pluralformen und sogar unregelmäßige Verben oder Substantive automatisch korrigiert. Bei einigen Abfragen, z. B. der Fuzzy- oder Platzhaltersuche, ist die lexikalische Analyse nicht Teil der Pipeline für die Abfrageanalyse. Für einige Szenarien wurden reguläre Ausdrücke zur Problemumgehung verwendet. 

### <a name="paging-results"></a>Auslagerungsergebnisse
Mit der kognitiven Azure-Suche ist es einfach, das Paging (Anordnen auf Seiten) von Suchergebnissen zu implementieren. Mit den Parametern **`top`** und **`skip`** können Sie reibungslos Suchanforderungen ausgeben, die Ihnen das Empfangen aller Suchergebnisse in Form von verwaltbaren, sortierten Teilmengen und somit eine benutzerfreundliche Vorgehensweise auf der Suchoberfläche ermöglichen. Wenn Sie diese kleineren Teilmengen mit Ergebnissen empfangen, können Sie auch die Zahl der Dokumente in der Gesamtmenge der Suchergebnisse erhalten.

Weitere Informationen zum Paging von Suchergebnissen finden Sie im Artikel [Anordnen von Suchergebnissen auf Seiten in der kognitiven Azure-Suche](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Sortieren von Ergebnissen
Wenn Sie Ergebnisse für eine Suchabfrage erhalten, können Sie anfordern, dass die kognitive Azure-Suche die Ergebnisse sortiert nach den Werten in einem bestimmten Feld bereitstellt. Die kognitive Azure-Suche sortiert die Suchergebnisse standardmäßig basierend auf der Rangfolge der Suchbewertung eines Dokuments, die von [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)abgeleitet wird.

Falls die kognitive Azure-Suche die Ergebnisse nach einem anderen Wert als der Suchbewertung sortiert zurückgeben soll, können Sie den Suchparameter **`orderby`** verwenden. Sie können den Wert des Parameters **`orderby`** angeben, um Feldnamen und Aufrufe der [ **`geo.distance()`-Funktion**](query-odata-filter-orderby-syntax.md) für räumliche Werte einzubinden. Auf jeden Ausdruck kann `asc` folgen, um anzugeben, dass die Ergebnisse in aufsteigender Reihenfolge angefordert werden. Verwenden Sie **`desc`** , um anzugeben, dass die Ergebnisse in absteigender Reihenfolge zurückgegeben werden sollen. Standardmäßig wird die aufsteigende Sortierung verwendet.


### <a name="hit-highlighting"></a>Treffermarkierung
In der kognitiven Azure-Suche ist das Hervorheben der exakten Menge von Suchergebnissen, die mit der Suchabfrage übereinstimmen, dank der Parameter **`highlight`** , **`highlightPreTag`** und **`highlightPostTag`** ganz einfach. Sie können angeben, für welche *durchsuchbaren* Felder der gefundene Text hervorgehoben werden soll. Außerdem können Sie die genauen Zeichenfolgentags angeben, die am Anfang und Ende des von der kognitiven Azure-Suche zurückgegebenen Übereinstimmungstexts angefügt werden sollen.

## <a name="see-also"></a>Weitere Informationen

+ [Funktionsweise der Volltextsuche in der kognitiven Azure-Suche (Architektur für Abfrageanalyse)](search-lucene-query-architecture.md)
+ [Suchexplorer](search-explorer.md)
+ [Abfragen des Azure Search-Index mit dem .NET SDK](./search-get-started-dotnet.md)
+ [Abfragen des Azure Search-Index mit der REST-API](./search-get-started-powershell.md)