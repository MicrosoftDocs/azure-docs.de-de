---
title: Erstellen einer Abfrage
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie in Cognitive Search eine Abfrageanforderung erstellen, welche Tools und APIs Sie für Tests und beim Programmieren verwenden können und welchen Einfluss der Indexentwurf auf Abfrageentscheidungen hat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: b013c66feefade077c85194ba3b1ff04ff4c4aa5
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536831"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Erstellen von Abfragen in Azure Cognitive Search

Wenn Sie zum ersten Mal eine Abfrage erstellen, finden Sie in diesem Artikel Ansätze und Methoden zum Einrichten von Abfragen. Außerdem wird eine Abfrageanforderung vorgestellt und erklärt, wie Feldattribute und linguistische Analysemodule die Abfrageergebnisse beeinflussen können.

## <a name="whats-a-query-request"></a>Was ist eine Abfrageanforderung?

Eine Abfrage ist eine Nur-Lese-Anforderung an die Dokumentsammlung eines einzelnen Suchindexes. Sie gibt ein queryType-Element und einen Abfrageausdruck durch den search-Parameter an. Der Abfrageausdruck kann Suchbegriffe, einen in Anführungszeichen angegebenen Ausdruck und Operatoren enthalten.

Eine Abfrage kann auch ein count-Element enthalten, um die Anzahl der gefundenen Übereinstimmungen im Index zurückzugeben, ein select-Element zum Auswählen der im Suchergebnis zurückgegebenen Felder sowie ein orderby-Element, um die Ergebnisse zu sortieren. Das folgende Beispiel zeigt eine Teilmenge der verfügbaren Parameter, um Ihnen eine allgemeine Vorstellung von einer Abfrageanforderung zu geben. Weitere Informationen zur Abfragekomposition finden Sie unter [Abfragetypen und -kompositionen](search-query-overview.md) sowie [Dokumente durchsuchen (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
    "search": "`New York` +restaurant",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "count": "true",
    "orderby": "Rating desc"
}
```

## <a name="choose-a-client"></a>Auswählen eines Clients

Sie benötigen ein Tool (z. B. Azure-Portal oder Postman) oder Code, der einen Abfrageclient mithilfe von APIs instanziiert. Für die anfängliche Entwicklung sowie für Proof of Concept-Tests empfiehlt sich die Verwendung des Azure-Portals oder der REST-APIs.

### <a name="permissions"></a>Berechtigungen

Alle Vorgänge, einschließlich Abfrageanforderungen, können unter einem [Administrator-API-Schlüssel](search-security-api-keys.md) verwendet werden, aber Abfrageanforderungen können optional einen [Abfrage-API-Schlüssel](search-security-api-keys.md#create-query-keys) verwenden. Abfrage-API-Schlüssel werden dringend empfohlen. Sie können bis zu 50 Schlüssel pro Dienst erstellen und den verschiedenen Anwendungen unterschiedliche Schlüssel zuweisen.

Im Azure-Portal erfordert der Zugriff auf Tools, Assistenten und Objekte die Mitgliedschaft in der Rolle „Mitwirkender“ oder höher für den Dienst. 

### <a name="use-azure-portal-to-query-an-index"></a>Abfragen eines Indexes über das Azure-Portal

Der [Suchexplorer (Portal)](search-explorer.md) ist eine Abfrageschnittstelle im Azure-Portal, mit dem Sie Indizes im zugrunde liegenden Suchdienst abfragen können. Intern stellt das Portal [Dokumente durchsuchen](/rest/api/searchservice/search-documents)-Anforderungen, kann aber keine automatische Vervollständigung, keine Vorschläge und keine Dokumentensuche aufrufen. 

Sie können einen beliebigen Index und eine REST-API-Version auswählen (einschließlich der Vorschauversion). Für die Abfragezeichenfolge kann die einfache oder vollständige Syntax mit Unterstützung für alle Abfrageparameter (filter, select, searchFields usw.) verwendet werden. Wenn Sie im Portal einen Index öffnen, können Sie den Suchexplorer zusammen mit der JSON-Indexdefinition in parallelen Registerkarten nutzen, um einfacher auf Feldattribute zugreifen zu können. Überprüfen Sie beim Testen Ihrer Abfragen, welche Felder durchsuchbar, sortierbar, filterbar und facettierbar sind.

### <a name="use-a-rest-client"></a>Verwenden eines REST-Clients

Sowohl Postman als auch Visual Studio Code (mit einer Erweiterung für Azure Cognitive Search) kann als Abfrageclient fungieren. Mit beiden Tools können Sie eine Verbindung mit Ihrem Suchdienst herstellen und die Anforderung [Dokumente durchsuchen (REST)](/rest/api/searchservice/search-documents) senden. Das Abfragen von Indizes mithilfe von REST-Clients wird in zahlreichen Tutorials und Beispielen veranschaulicht. 

Weitere Informationen zum jeweiligen Client finden Sie in den folgenden Artikeln (beide enthalten Anweisungen für Abfragen):

+ [Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs](search-get-started-rest.md)
+ [Erste Schritte mit Visual Studio Code und Azure Cognitive Search](search-get-started-vs-code.md)

Jede Anforderung ist eigenständig. Daher müssen Sie den Endpunkt, den Indexnamen und die API-Version für jede Anforderung angeben. Andere Eigenschaften, der Inhaltstyp und die API-Schlüssel werden im Anforderungsheader weitergegeben. Weitere Informationen finden Sie unter [Dokumente durchsuchen (REST)](/rest/api/searchservice/search-documents), um Hilfe bei der Formulierung von Abfrageanforderungen zu erhalten.

### <a name="use-an-sdk"></a>Verwenden eines SDK

Für Cognitive Search werden von den Azure-SDKs allgemein verfügbare Features implementiert. Daher kann jedes der SDKs zum Abfragen eines Suchindex verwendet werden. Alle stellen einen **SearchClient** zur Verfügung, der über Methoden zur Interaktion mit einem Index verfügt, vom Laden eines Index mit Suchdokumenten bis hin zur Formulierung von Abfrageanforderungen.

| Azure SDK | Client | Beispiele |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [readonlyQuery.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query.py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Auswählen eines Abfragetyps: Einfach | vollständig

Wenn Ihre Abfrage eine Volltextsuche ist, wird ein Abfrageparser verwendet, um jeden Text zu verarbeiten, der als Suchbegriffe und -Ausdrücke übergeben wird. Azure Cognitive Search bietet zwei Abfrageparser. 

+ Der einfache Parser versteht die [einfache Abfragesyntax](query-simple-syntax.md). Dieser Parser wird standardmäßig verwendet, da er bei Abfragen von Freiformtext eine hohe Geschwindigkeit und Effizienz bietet. Die Syntax unterstützt gängige Suchoperatoren (AND, OR, NOT) für die Suche nach Begriffen und Ausdrücken und die Präfixsuche (`*`, wie in „Sea*“ für „Seattle“ und „Seaside“). Ganz allgemein wird empfohlen, zuerst den einfachen Parser auszuprobieren und nur dann zum vollständigen Parser zu wechseln, wenn Anwendungsanforderungen leistungsfähigere Abfragen erfordern.

+ Die [vollständige Lucene-Abfragesyntax](query-Lucene-syntax.md#bkmk_syntax), die beim Hinzufügen von `queryType=full` zur Anforderung aktiviert wird, basiert auf dem [Apache Lucene-Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Vollständige Syntax und einfache Syntax stimmen darin überein, dass beide dieselben Präfix- und booleschen Vorgänge unterstützen. Bei der vollständigen Syntax stehen Ihnen allerdings mehr Operatoren zur Verfügung. Beim vollständigen Parser gibt es mehr Operatoren für boolesche Ausdrücke und mehr Operatoren für komplexere Abfragen, wie z. B. die Fuzzysuche, die Platzhaltersuche, die NEAR-Suche und reguläre Ausdrücke.

## <a name="choose-query-methods"></a>Auswählen der Abfragemethoden

Die Suche ist im Grunde genommen ein vom Benutzer gesteuerter Vorgang, bei dem Begriffe oder Ausdrücke aus einem Suchfeld oder von Klickereignissen auf einer Seite gesammelt werden. In der folgenden Tabelle werden die Verfahren zusammengefasst, mit denen Sie Benutzereingaben erfassen können. Außerdem sind die erwarteten Suchvorgänge aufgeführt.

| Eingabe | Erfahrung |
|-------|---------|
| [Suche](/rest/api/searchservice/search-documents) | Benutzer geben Begriffe oder Ausdrücke (mit oder ohne Operatoren) in ein Suchfeld ein und klicken auf „Suchen“, um die Anforderung zu senden. Die Suche kann mit Filtern für dieselbe Anforderung, jedoch nicht mit AutoVervollständigen oder Vorschlägen verwendet werden. |
| [AutoVervollständigen](/rest/api/searchservice/autocomplete) | Benutzer geben einige Zeichen ein, und nach der Eingabe jedes neuen Zeichens werden Abfragen initiiert. Die Antwort ist eine vollständige Zeichenfolge aus dem Index. Wenn die bereitgestellte Zeichenfolge passend ist, klicken die Benutzer auf „Suchen“, um die Abfrage an den Dienst zu senden. |
| [Vorschläge](/rest/api/searchservice/suggestions) | Wie beim AutoVervollständigen werden während der Eingabe einiger Zeichen durch Benutzer inkrementelle Abfragen generiert. Die Antwort ist eine Dropdownliste mit übereinstimmenden Dokumenten, die in der Regel durch einige eindeutige oder beschreibende Felder dargestellt werden. Wenn eine der Optionen passend ist, klicken die Benutzer darauf, und das entsprechende Dokument wird zurückgegeben. |
| [Facettennavigation](/rest/api/searchservice/search-documents#query-parameters) | Auf einer Seite werden klickbare Navigationslinks oder „Brotkrümel“ (Breadcrumbs) angezeigt, die den Suchbereich eingrenzen. Die Struktur der Facettennavigationsstruktur wird dynamisch basierend auf einer anfänglichen Abfrage gebildet. Mit `search=*` wird z. B. eine Facettennavigationsstruktur aufgefüllt, die alle möglichen Kategorien umfasst. Die Struktur bei einer Facettennavigation wird aus einer Abfrageantwort erstellt, sie dient aber auch zum Ausdrücken der nächsten Abfrage. In der REST-API-Referenz ist `facets` als Abfrageparameter eines Vorgangs „Dokumente suchen“ dokumentiert, er kann jedoch auch ohne den `search`-Parameter verwendet werden.|
| [Filtermethode](/rest/api/searchservice/search-documents#query-parameters) | Mithilfe von Filtern können Sie die Ergebnisse von Facetten eingrenzen. Sie können auch einen Filter im Hintergrund der Seite implementieren, um die Seite z. B. mit sprachspezifischen Feldern zu initialisieren. In der REST-API-Referenz ist `$filter` als Abfrageparameter eines Vorgangs „Dokumente suchen“ dokumentiert, er kann jedoch auch ohne den `search`-Parameter verwendet werden.|

## <a name="know-your-field-attributes"></a>Verstehen der Feldattribute

Wenn Sie sich bereits mit den [Abfragetypen und -komposition](search-query-overview.md) vertraut gemacht haben, erinnern Sie sich möglicherweise daran, dass die Parameter in der Abfrageanforderung davon abhängen, wie Feldern in einem Index Attribute zugeordnet werden. Damit ein Feld beispielsweise in einer Abfrage, einem Filter oder einer Sortierreihenfolge verwendet werden kann, muss es *durchsuchbar*, *filterbar* und *sortierbar* sein. Darüber hinaus können in Ergebnissen nur Felder angezeigt werden, die als *abrufbar* gekennzeichnet sind. Wenn Sie damit beginnen, in Ihrer Anforderung die Parameter `search`, `filter` und `orderby` anzugeben, sollten Sie unbedingt die Attribute überprüfen, um unerwartete Ergebnisse zu vermeiden.

Im folgenden Screenshot des Portals mit dem [Beispielindex für Hotels](search-get-started-portal.md) können nur die letzten beiden Felder „LastRenovationDate“ und „Rating“ in einer `"$orderby"`-Klausel verwendet werden.

![Indexdefinition für das „hotels“-Beispiel](./media/search-query-overview/hotel-sample-index-definition.png "Indexdefinition für das „hotels“-Beispiel")

Eine Beschreibung der Feldattribute finden Sie unter [Erstellen eines Index (REST-API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Verstehen der Token

Während der Indizierung verwendet die Suchmaschine ein Analysetool, um Textanalysen für Zeichenfolgen durchzuführen und so die Wahrscheinlichkeit von Übereinstimmungen zur Abfragezeit zu maximieren. Zeichenfolgen sind mindestens kleingeschrieben. Sie können aber auch eine Lemmatisierung und Stoppwortentfernung durchlaufen. Größere Zeichenfolgen oder zusammengesetzte Wörter werden in der Regel an Leerzeichen, Bindestrichen oder Gedankenstrichen aufgeteilt und als separate Token indiziert. 

Sie sollten also immer davon ausgehen, dass der Index etwas anderes enthält, als Sie erwarten. Wenn Ihre Abfragen nicht die erwarteten Ergebnisse zurückgeben, können Sie die vom Analysetool erstellten Token mit [Text analysieren (REST-API)](/rest/api/searchservice/test-analyzer) überprüfen. Weitere Informationen zur Tokenisierung und ihrer Auswirkungen auf Abfragen finden Sie unter [Suche nach Teilausdrücken und Mustern mit Sonderzeichen](search-query-partial-matching.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun ein besseres Verständnis von der Funktionsweise von Abfrageanforderungen haben, können Sie in den folgenden Schnellstarts praktische Erfahrungen sammeln.

+ [Suchexplorer](search-explorer.md)
+ [Abfragen des Azure Search-Index mit der REST-API](search-get-started-rest.md)
+ [Abfragen des Azure Search-Index mit dem .NET SDK](search-get-started-dotnet.md)
+ [Erstellen eines Azure Cognitive Search-Index in Python mithilfe von Jupyter Notebook-Instanzen](search-get-started-python.md)
+ [Erstellen eines Index für Azure Cognitive Search mit dem JavaScript-SDK](search-get-started-javascript.md)