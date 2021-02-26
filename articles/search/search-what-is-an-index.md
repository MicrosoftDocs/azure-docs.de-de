---
title: Erstellen eines Index
titleSuffix: Azure Cognitive Search
description: Hier finden Sie eine Einführung in die Indizierungskonzepte und -tools in Azure Cognitive Search, einschließlich Schemadefinitionen und der physischen Datenstruktur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d0cc7630a3bea67a99c3cb65d2015e934e8ac2da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539093"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Erstellen von Suchindizes in Azure Cognitive Search

In Cognitive Search speichert ein *Suchindex* durchsuchbaren Inhalt, der für Volltext- und gefilterte Abfragen verwendet wird. Ein Index wird durch ein Schema definiert und im Dienst gespeichert. Der Datenimport erfolgt in einem zweiten Schritt. 

Indizes enthalten *Suchdokumente*. Konzeptionell ist ein Dokument eine einzelne Einheit mit durchsuchbaren Daten im Index. Ein Einzelhändler verfügt beispielsweise über ein Dokument für jedes Produkt, eine Nachrichtenagentur über ein Dokument pro Zeitungsartikel usw. So lassen sich diese Konzepte vertrauteren Entsprechungen in der Datenbank zuordnen: Ein *Suchindex* entspricht einer *Tabelle*, und *Dokumente* entsprechen ungefähr den *Zeilen* einer Tabelle.

## <a name="whats-an-index-schema"></a>Was ist ein Indexschema?

Die physische Struktur eines Index wird durch das Schema bestimmt. Die „Feldsammlung“ ist typischerweise der größte Teil eines Index, wobei jedes Feld benannt ist, einen [Datentyp](/rest/api/searchservice/Supported-data-types) erhält und mit zulässigen Verhaltensweisen versehen wird, die bestimmen, wie es verwendet wird.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Weitere Elemente sind aus Gründen der Übersicht reduziert, über die folgenden Links erhalten Sie jedoch weitere Informationen: [Vorschlagsfunktionen](index-add-suggesters.md), [Bewertungsprofile](index-add-scoring-profiles.md) und [Analysetools](search-analyzers.md), die zum Verarbeiten von Zeichenfolgen in Token gemäß den linguistischen Regeln oder anderen vom Analysetool unterstützten Merkmalen verwendet werden, sowie [CORS-Einstellungen (Cross-Origin Remote Scripting)](#corsoptions).

## <a name="choose-a-client"></a>Auswählen eines Clients

Es gibt mehrere Methoden zum Erstellen eines Suchindex. Für die anfängliche Entwicklung sowie für Proof of Concept-Tests empfiehlt sich die Verwendung des Azure-Portals oder der REST-APIs.

Planen Sie während der Entwicklung häufige Neuerstellungen ein. Da physische Strukturen im Dienst erstellt werden, ist das [Löschen und Neuerstellen von Indizes](search-howto-reindex.md) bei den meisten Änderungen an einer vorhandenen Felddefinition erforderlich. Sie sollten erwägen, mit einer Teilmenge Ihrer Daten zu arbeiten, damit Neuerstellungen schneller gehen.

### <a name="permissions"></a>Berechtigungen

Alle Vorgänge im Zusammenhang mit Suchindizes, einschließlich GET-Anforderungen für die Definitionen, erfordern einen [Admin-API-Schlüssel](search-security-api-keys.md) in der Anforderung.

### <a name="limits"></a>Einschränkungen

Die Anzahl von Objekten, die Sie erstellen können, sind bei allen Dienstebenen [begrenzt](search-limits-quotas-capacity.md#index-limits). Wenn Sie mit dem Free-Tarif experimentieren, können Sie jeweils nur drei Indizes haben.

### <a name="use-azure-portal-to-create-a-search-index"></a>Erstellen eines Suchindex im Azure-Portal

Im Portal stehen zwei Optionen für die Erstellung eines Suchindex zur Verfügung: [**Datenimport-Assistent**](search-import-data-portal.md) und **Index hinzufügen** mit Feldern zum Angeben eines Indexschemas. Der Assistent integriert zusätzliche Vorgänge, indem er außerdem einen Indexer und eine Datenquelle erstellt und Daten lädt. Wenn Sie diesen Umfang nicht benötigen, sollten Sie einfach **Index hinzufügen** oder einen anderen Ansatz verwenden.

Der folgende Screenshot zeigt, wo Sie **Index hinzufügen** im Portal finden. **Daten importieren** ist direkt daneben zu finden.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Befehl „Index hinzufügen“" border="true":::

> [!Tip]
> Bei der Indexerstellung über das Portal werden Anforderungen und Schemaregeln für bestimmte Datentypen erzwungen, z. B. das Deaktivieren von Funktionen zur Volltextsuche bei numerischen Feldern. Wenn Sie über einen funktionierenden Index verfügen, können Sie den JSON-Code aus dem Portal kopieren und Ihrer Lösung hinzufügen.

### <a name="use-a-rest-client"></a>Verwenden eines REST-Clients

Sowohl Postman als auch Visual Studio Code (mit einer Erweiterung für Azure Cognitive Search) kann als Suchindexclient fungieren. Mit beiden Tools können Sie eine Verbindung mit Ihrem Suchdienst herstellen und die Anforderung [Index erstellen (REST)](/rest/api/searchservice/create-index) senden. Die Objekterstellung mithilfe von REST-Clients wird in zahlreichen Tutorials und Beispielen veranschaulicht. 

Weitere Informationen zum jeweiligen Client finden Sie in den folgenden Artikeln:

+ [Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs](search-get-started-rest.md)
+ [Erste Schritte mit Visual Studio Code und Azure Cognitive Search](search-get-started-vs-code.md)

Hilfreiche Informationen zum Formulieren von Indexanforderungen finden Sie unter [Indexvorgänge (REST)](/rest/api/searchservice/index-operations).

### <a name="use-an-sdk"></a>Verwenden eines SDK

Für Cognitive Search werden von den Azure-SDKs allgemein verfügbare Features implementiert. Daher kann jedes der SDKs für die Erstellung eines Suchindex verwendet werden. Alle bieten einen **SearchIndexClient**, der über Methoden zum Erstellen und Aktualisieren von Indizes verfügt.

| Azure SDK | Client | Beispiele |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Indizes](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>Definieren von Feldern

Ein Suchdokument wird durch die `fields`-Sammlung definiert. Sie benötigen Felder für Abfragen und Schlüssel. Sie benötigen wahrscheinlich auch Felder, um Filter, Facets und Sortierungen zu unterstützen. Möglicherweise benötigen Sie auch Felder für Daten, die Benutzern niemals angezeigt werden. Sie können z. B. Felder für Gewinnmargen oder Marketingaktionen verwenden, mit denen Sie den Suchrang anpassen können.

Ein Feld vom Typ „Edm.String“ muss als Dokumentschlüssel angegeben werden. Es wird für die eindeutige Identifizierung der einzelnen Suchdokumente verwendet. Sie können ein Dokument anhand seines Schlüssels abrufen, um eine Detailseite aufzufüllen.  

Wenn eingehende Daten hierarchisch sind, weisen Sie einen [komplexen Typ](search-howto-complex-data-types.md) zu, um die geschachtelten Strukturen darzustellen. Das integrierte Beispieldataset „Hotels“ veranschaulicht komplexe Typen anhand einer Adresse (enthält mehrere untergeordnete Felder) mit einer 1:1-Beziehung zu den einzelnen Hotels verwenden und einer komplexen Zimmersammlung, wobei jedem Hotel mehrere Zimmer zugeordnet sind. 

Weisen Sie alle Analysetools Zeichenfolgenfeldern zu, bevor der Index erstellt wird. Führen Sie die gleichen Schritte für Vorschlagsfunktionen aus, wenn Sie AutoVervollständigen für bestimmte Felder aktivieren möchten.

<a name="index-attributes"></a>

### <a name="attributes"></a>Attributes

Feldattribute bestimmen, wie ein Feld verwendet wird, z.B. ob es in der Volltextsuche, in der Facettennavigation, für Sortiervorgänge usw. verwendet wird. 

Zeichenfolgenfelder werden häufig als „durchsuchbar“ und „abrufbar“ gekennzeichnet. Zu den Feldern zum Eingrenzen der Suchergebnisse gehören „sortierbar“, „filterbar“ und „facettenreich“.

|Attribut|BESCHREIBUNG|  
|---------------|-----------------|  
|„durchsuchbar“ |Volltextsuche ist möglich, unterliegt einer lexikalischen Analyse, z.B. Worttrennung, während der Indizierung. Wenn Sie ein durchsuchbares Feld auf einen Wert wie „sunny day“ festlegen, wird es intern in die einzelnen Token „sunny“ und „day“ unterteilt. Weitere Informationen finden Sie unter [Funktionsweise der Volltextsuche](search-lucene-query-architecture.md).|  
|„filterbar“ |Wird in $filter-Abfragen angegeben. Filterbare Felder vom Typ `Edm.String` oder `Collection(Edm.String)` werden keiner Worttrennung unterzogen, sodass nur nach exakten Übereinstimmungen gesucht wird. Beispiel: Wenn Sie ein solches Feld „f“ auf „sunny day“ festlegen, werden mit `$filter=f eq 'sunny'` keine Übereinstimmungen gefunden, während Sie mit `$filter=f eq 'sunny day'` Suchergebnisse erhalten. |  
|„sortierbar“ |Standardmäßig sortiert das System Ergebnisse nach Bewertung, aber Sie können die Sortierung auf Grundlage von Feldern in den Dokumenten konfigurieren. Felder vom Typ `Collection(Edm.String)` können nicht „sortierbar“ sein. |  
|„facettenreich“ |Wird in der Regel in einer Darstellung der Suchergebnisse verwendet, die eine Trefferanzahl nach Kategorie umfasst (z.B. Hotels in einer bestimmten Stadt). Diese Option kann nicht für Felder vom Typ `Edm.GeographyPoint` verwendet werden. Felder vom Typ `Edm.String`, die „filterbar“, „sortierbar“ oder „facettenreich“ sind, dürfen höchstens 32 KB lang sein. Weitere Details finden Sie unter [Erstellen des Index (REST API)](/rest/api/searchservice/create-index).|  
|„Schlüssel“ |Eindeutiger Bezeichner für Dokumente im Index. Es muss genau ein Feld als Schlüsselfeld ausgewählt werden, und es muss vom Typ `Edm.String` sein.|  
|„abrufbar“ |Legt fest, ob das Feld in einem Suchergebnis zurückgegeben werden kann. Dies ist hilfreich, wenn Sie ein Feld (z.B. *Gewinnspanne*) zum Filtern, Sortieren oder Bewerten verwenden möchten, das Feld jedoch für den Endbenutzer nicht sichtbar sein soll. Dieses Attribut muss für `key`-Felder auf `true` gesetzt sein.|  

Obwohl Sie jederzeit neue Felder hinzufügen können, sind vorhandene Felddefinitionen für die Lebensdauer des Index gesperrt. Aus diesem Grund verwenden Entwickler in der Regel das Portal zum Erstellen einfacher Indizes und zum Testen von Ideen. Oder sie verwenden Seiten des Portals, um eine Einstellung zu suchen. Häufige Iterationen über einen Indexentwurf sind effizienter, wenn Sie einem codebasierten Ansatz folgen, sodass Sie den Index einfach neu erstellen können.

> [!NOTE]
> Die APIs, die Sie zum Erstellen eines Index verwenden, verfügen über unterschiedliches Standardverhalten. Für die [REST-APIs](/rest/api/searchservice/Create-Index) sind die meisten Attribute standardmäßig aktiviert (z. B. sind „durchsuchbar“ und „abrufbar“ für Zeichenfolgenfelder auf TRUE festgelegt). Sie müssen sie häufig nur festlegen, wenn Sie sie deaktivieren möchten. Für das .NET SDK gilt das Gegenteil. Für jede Eigenschaft, die Sie nicht explizit festlegen, wird das entsprechende Suchverhalten standardmäßig deaktiviert, sofern Sie es nicht ausdrücklich aktivieren.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attribute und Indexgröße (Auswirkungen auf den Speicher)

Die Größe eines Indexes wird durch die Größe der von Ihnen hochgeladenen Dokumente sowie durch die Indexkonfiguration bestimmt, also beispielsweise dadurch, ob Sie Vorschlagsfunktionen einbeziehen und wie Sie Attribute für einzelne Felder festlegen. 

Der folgende Screenshot veranschaulicht die Indexspeichermuster aus verschiedenen Kombinationen von Attributen. Der Index basiert auf dem **Real Estate-Beispielindex**, den Sie ganz einfach mithilfe des Datenimport-Assistenten erstellen können. Obwohl die Indexschemas nicht angezeigt werden, können Sie die auf dem Indexnamen basierenden Attribute ableiten. Beispielsweise ist für den *realestate-searchable*-Index das searchable-Attribut ausgewählt und sonst nichts, für den *realestate-retrievable*-Index ist das retrievable-Attribut ausgewählt und nichts anderes usw.

![Indexgröße basierend auf der Attributauswahl](./media/search-what-is-an-index/realestate-index-size.png "Indexgröße basierend auf der Attributauswahl")

Obwohl diese Indexvarianten künstlich sind, können wir sie für umfassende Vergleiche des Einflusses von Attributen auf Speicher nutzen. Setzt die Einstellung „abrufbar“ die Indexgröße herauf? Nein. Setzt das Hinzufügen von Feldern zu einer **Vorschlagsfunktion** die Indexgröße herauf? Ja. 

Wenn Sie ein Feld filterbar oder sortierbar machen, erhöht dies auch den Speicherverbrauch, da gefilterte und sortierte Felder nicht tokenisiert werden, sodass Zeichensequenzen ausführlich abgeglichen werden können.

Außerdem werden in der obigen Tabelle auch die Auswirkungen von [Analysetools](search-analyzers.md) nicht berücksichtigt. Wenn Sie den Tokenizer edgeNgram zum Speichern von direkten Zeichensequenzen (a, ab, abc, abcd) verwenden, ist der Index größer als bei Verwendung eines Standardanalysetools.

> [!Note]
> Die Speicherarchitektur gilt als Implementierungsdetail der kognitiven Azure-Suche und kann ohne vorherige Ankündigung geändert werden. Es gibt keine Garantie, dass das aktuelle Verhalten in der Zukunft beibehalten wird.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Informationen zum `corsOptions`

Indexschemas enthalten einen Abschnitt zum Festlegen von `corsOptions`. Clientseitiger JavaScript-Code kann standardmäßig keine APIs aufrufen, da der Browser jegliche ursprungsübergreifenden Anforderungen verhindert. Um ursprungsübergreifende Abfragen für Ihren Index zu ermöglichen, aktivieren Sie CORS (Cross-Origin Resource Sharing), indem Sie das Attribut **corsOptions** festlegen. Aus Sicherheitsgründen wird CORS nur von Abfrage-APIs unterstützt. 

Die folgenden Optionen können für CORS festgelegt werden:

+ **allowedOrigins** (erforderlich): Dies ist eine Liste der Ursprünge, denen Zugriff auf Ihren Index gewährt wird. Dies bedeutet, dass jeglicher von diesen Ursprüngen bereitgestellte Javascript-Code Indexabfragen durchführen kann (sofern er den richtigen API-Schlüssel angibt). Ursprünge werden in der Regel im Format `protocol://<fully-qualified-domain-name>:<port>` angegeben, wobei `<port>` häufig weggelassen wird. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing, CORS (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

  Wenn Sie den Zugriff auf alle Ursprünge zulassen möchten, beziehen Sie `*` als ein einzelnes Element in das Array **allowedOrigins** ein. *Dies ist keine empfohlene Vorgehensweise für Produktionssuchdienste*, ist für Entwicklung und Debugging jedoch oft nützlich.

+ **maxAgeInSeconds** (optional): Von Browsern wird dieser Wert verwendet, um die Dauer (in Sekunden) des Zwischenspeicherns von CORS-Preflight-Antworten zu ermitteln. Dies muss eine positive ganze Zahl sein. Mit dem Wert steigt auch die Leistung, aber es dauert auch länger, bis CORS-Richtlinienänderungen in Kraft treten. Wenn diese Einstellung nicht festgelegt ist, gilt die Standarddauer von 5 Minuten.

## <a name="next-steps"></a>Nächste Schritte

Praktische Erfahrungen mit der Erstellung eines Index können Sie mit fast jedem Beispiel oder jeder exemplarischen Vorgehensweise für Cognitive Search sammeln. Als Einstieg können Sie einen der Schnellstarts im Inhaltsverzeichnis auswählen.

Sie sollten sich aber auch mit Methoden zum Laden eines Index mit Daten vertraut machen. Strategien zur Indexdefinition und zum Datenimport werden zusammen definiert. Die folgenden Artikel bieten Informationen zum Laden eines Index.

+ [Übersicht über den Datenimport](search-what-is-data-import.md)

+ [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 