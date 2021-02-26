---
title: Erstellen eines Indexers
titleSuffix: Azure Cognitive Search
description: Legen Sie Eigenschaften für einen Indexer fest, um den Ursprung und die Ziele von Daten zu bestimmen. Durch Festlegen von Parametern können Sie das Verhalten zur Laufzeit ändern.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 5fc47599d09e5be60311dbda15868d87de4d91d2
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509383"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Erstellen von Indexern in Azure Cognitive Search

Ein Suchindexer bietet einen automatisierten Workflow, um Dokumente und Inhalte aus einer externen Datenquelle in einen Suchindex für Ihren Suchdienst zu übertragen. In seiner ursprünglichen Form extrahiert er Text und Metadaten aus einer Azure-Datenquelle, serialisiert Dokumente in JSON und übergibt die resultierenden Dokumente zur Indizierung an eine Suchmaschine. Inzwischen unterstützt er auch die [KI-Anreicherung](cognitive-search-concept-intro.md) für eine eingehende Inhaltsverarbeitung. 

Der Einsatz von Indexern trägt erheblich zur Verringerung der Menge und Komplexität des benötigten Codes bei. Dieser Artikel konzentriert sich auf die Mechanismen für die Erstellung eines Indexers als Vorbereitung auf komplexere Arbeiten mit quellspezifischen Indexern und [Skillsets](cognitive-search-working-with-skillsets.md).

## <a name="whats-an-indexer-definition"></a>Was ist eine Indexerdefinition?

Indexer können für eine textbasierte Indizierung verwendet werden, bei der alphanumerische Inhalte aus Quellfeldern in Indexfelder gepullt werden. Sie können aber auch für eine KI-basierte Verarbeitung genutzt werden, bei der die Struktur von undifferenziertem Text analysiert oder Bilder auf Text und Informationen überprüft werden und dieser Inhalt dann ebenfalls einem Index hinzugefügt wird. Die folgenden Indexerdefinitionen sind typische Beispiele für Definitionen, die für diese Szenarien erstellt werden.

### <a name="indexers-for-text-content"></a>Indexer für Textinhalt

Indexer waren ursprünglich dafür gedacht, den komplexen Prozess des Ladens eines Index zu vereinfachen. Der bereitgestellte Mechanismus ermöglicht es, eine Verbindung mit einer Datenquelle herzustellen, Text und numerische Inhalte aus Feldern in der Datenquelle zu lesen, die Inhalte zu JSON-Dokumenten zu serialisieren und diese Dokumente anschließend zur Indizierung an die Suchmaschine zu übergeben. Dies ist nach wie vor einer der gängigsten Anwendungsfälle, und für diesen Vorgang muss ein Indexer mit den Eigenschaften aus dem folgenden Beispiel erstellt werden.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

Die Eigenschaften **`name`** , **`dataSourceName`** und **`targetIndexName`** sind erforderlich, und abhängig davon, wie Sie den Indexer erstellen, müssen die Datenquelle und der Index ggf. bereits im Dienst vorhanden sein, damit der Indexer ausgeführt werden kann. 

Die Eigenschaft **`parameters`** dient zum Ändern des Verhaltens zur Laufzeit. Mit ihr können Sie beispielsweise steuern, wie viele Fehler akzeptiert werden, bevor der gesamte Auftrag als fehlerhaft betrachtet wird. Mit Parametern können Sie auch quellspezifisches Verhalten angeben. Handelt es sich bei der Quelle beispielsweise um einen Blobspeicher, können Sie einen Parameter zum Filtern nach Dateierweiterungen festlegen: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }`.

Die Eigenschaft **`field mappings`** dient zur expliziten Zuordnung von Quell- und Zielfeldern, wenn sich der Name oder Typ dieser Felder unterscheidet. Andere Eigenschaften (nicht gezeigt) ermöglichen das [Angeben eines Zeitplans](search-howto-schedule-indexers.md), das Erstellen eines deaktivierten Indexers oder das Angeben eines [Verschlüsselungsschlüssels](search-security-manage-encryption-keys.md) zur ergänzenden Verschlüsselung ruhender Daten.

### <a name="indexers-for-ai-indexing"></a>Indexer für die KI-Indizierung

Da es sich bei Indexern um den Mechanismus handelt, mit dem ein Suchdienst ausgehende Anforderungen durchführt, wurden Indexer erweitert, um KI-Anreicherungen zu unterstützen und Infrastruktur sowie Objekte zur Implementierung dieses Anwendungsfalls hinzuzufügen.

Alle oben angegebenen Eigenschaften und Parameter gelten auch für Indexer mit KI-Anreicherung. Bei den folgenden Eigenschaften handelt es sich um spezifische Eigenschaften für die KI-Anreicherung: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (nur Vorschauversion und REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

Die KI-Anreicherung wird in diesem Artikel nicht behandelt. Weitere Informationen finden Sie in den folgenden Artikeln: [KI-Anreicherung in Azure Cognitive Search](cognitive-search-concept-intro.md), [Skillsetkonzepte in Azure Cognitive Search](cognitive-search-working-with-skillsets.md) und [Skillset erstellen (Azure Cognitive Search-REST-API)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Auswählen eines Indexerclients und Erstellen des Indexers

Wenn Sie einen Indexer für einen Remotesuchdienst erstellen möchten, benötigen Sie einen Suchclient in Form eines Tools (beispielsweise das Azure-Portal oder Postman) oder Code, der einen Indexerclient instanziiert. Für die anfängliche Entwicklung sowie für Proof of Concept-Tests empfiehlt sich die Verwendung des Azure-Portals oder der REST-APIs.

### <a name="permissions"></a>Berechtigungen

Alle Vorgänge im Zusammenhang mit Indexern, einschließlich GET-Anforderungen für den Status oder Definitionen, erfordern einen [Admin-API-Schlüssel](search-security-api-keys.md) in der Anforderung.

### <a name="limits"></a>Einschränkungen

Die Anzahl von Objekten, die Sie erstellen können, sind bei allen Dienstebenen [begrenzt](search-limits-quotas-capacity.md#indexer-limits). Wenn Sie mit dem Free-Tarif experimentieren, können Sie nur drei Objekte von jedem Typ verwenden, und die Indexerverarbeitung ist auf zwei Minuten begrenzt (ohne Skillsetverarbeitung).

### <a name="use-azure-portal-to-create-an-indexer"></a>Erstellen eines Indexers über das Azure-Portal

Im Portal stehen zwei Optionen für die Indexererstellung zur Verfügung: [**Datenimport-Assistent**](search-import-data-portal.md) und **New Indexer** (Neuer Indexer) mit Feldern zum Angeben einer Indexerdefinition. Das Besondere an dem Assistenten ist, dass er alle erforderlichen Elemente erstellt. Bei anderen Ansätzen müssen eine vordefinierte Datenquelle und ein vordefinierter Index vorhanden sein.

Der folgende Screenshot zeigt, wo sich diese Features im Portal befinden: 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="Hotelindexer" border="true":::

### <a name="use-a-rest-client"></a>Verwenden eines REST-Clients

Sowohl Postman als auch Visual Studio Code (mit einer Erweiterung für Azure Cognitive Search) kann als Indexerclient fungieren. Mit beiden Tools können Sie eine Verbindung mit Ihrem Suchdienst herstellen und die Anforderung [Indexer erstellen (REST)](/rest/api/searchservice/create-indexer) senden. Die Objekterstellung mithilfe von REST-Clients wird in zahlreichen Tutorials und Beispielen veranschaulicht. 

Weitere Informationen zum jeweiligen Client finden Sie in den folgenden Artikeln:

+ [Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs](search-get-started-rest.md)
+ [Erste Schritte mit Visual Studio Code und Azure Cognitive Search](search-get-started-vs-code.md)

Hilfreiche Informationen zum Formulieren von Indexeranforderungen finden Sie unter [Indexervorgänge (Azure Cognitive Search-REST-API)](/rest/api/searchservice/Indexer-operations).

### <a name="use-an-sdk"></a>Verwenden eines SDK

Für Cognitive Search werden von den Azure-SDKs allgemein verfügbare Features implementiert. Daher kann jedes der SDKs für die Erstellung indexerbezogener Objekte verwendet werden. Alle stellen die Klasse **SearchIndexerClient** bereit, die über Methoden zum Erstellen von Indexern und zugehörigen Objekten (einschließlich Skillsets) verfügt.

| Azure SDK | Client | Beispiele |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexer](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Ausführen des Indexers

Ein Indexer wird automatisch ausgeführt, wenn Sie den Indexer für den Dienst erstellen. Dann zeigt sich, ob Datenquellenverbindungsfehler, Probleme mit der Feldzuordnung oder Skillsetprobleme vorhanden sind. 

Ein Indexer kann auf verschiedene Arten ausgeführt werden:

+ Sie können eine HTTP-Anforderung zum [Erstellen eines Indexers](/rest/api/searchservice/create-indexer) oder [Aktualisieren eines Indexers](/rest/api/searchservice/update-indexer) senden, um die Definition hinzuzufügen oder zu ändern und den Indexer auszuführen.

+ Sie können eine HTTP-Anforderung zum [Ausführen eines Indexers](/rest/api/searchservice/run-indexer) senden, um einen Indexer ohne Definitionsänderungen auszuführen.

+ Sie können ein Programm ausführen, das SearchIndexerClient-Methoden zum Erstellen, Aktualisieren oder Ausführen aufruft.

> [!NOTE]
> Soll der Indexer nicht sofort nach der Erstellung ausgeführt werden, schließen Sie **`disabled=true`** in die Indexerdefinition ein.

Alternativ können Sie für den Indexer [einen Zeitplan](search-howto-schedule-indexers.md) verwenden, um die Verarbeitung in regelmäßigen Abständen auszulösen. 

Bei der geplanten Verarbeitung ist in der Regel auch eine inkrementelle Indizierung geänderter Inhalte erforderlich. Änderungserkennungslogik ist in Quellplattformen integriert. Änderungen in einem Blobcontainer werden vom Indexer automatisch erkannt. Informationen zur Nutzung der Änderungserkennung in anderen Datenquellen finden Sie in der Indexerdokumentation für bestimmte Datenquellen:

+ [Azure SQL-Datenbank](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>Verstehen Ihrer Daten

Von Indexern wird ein tabellarisches Rowset erwartet. Die einzelnen Zeilen werden dann jeweils zu einem vollständigen oder partiellen Suchdokument im Index. Häufig besteht eine 1:1-Entsprechung zwischen einer Zeile und dem resultierenden Suchdokument, sodass alle Felder im Rowset das jeweilige Dokument vollständig auffüllen. Sie können jedoch auch Indexer verwenden, um nur einen Teil eines Dokuments zu generieren – etwa bei Verwendung mehrerer Indexer oder Ansätze zum Ausbau des Index. 

Wenn Sie relationale Daten zu einem Rowset vereinfachen möchten, sollten Sie eine SQL-Sicht erstellen (oder eine Abfrage, die übergeordnete und untergeordnete Datensätze in der gleichen Zeile zurückgibt). Bei dem integrierten Hotel-Beispieldataset handelt es sich beispielsweise um eine SQL-Datenbank mit 50 Datensätzen (jeweils einer pro Hotel), die mit Zimmerdatensätzen in einer verwandten Tabelle verknüpft sind. Die Abfrage, mit der die gesamten Daten zu einem Rowset vereinfacht werden, bettet alle Zimmerinformationen in JSON-Dokumenten in den jeweiligen Hoteldatensatz ein. Die eingebetteten Zimmerinformationen werden durch eine Abfrage mit einer Klausel vom Typ **FOR JSON AUTO** generiert. Weitere Informationen zu dieser Technik finden Sie unter [Definieren einer Abfrage, die eingebetteten JSON-Code zurückgibt](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Dies ist nur eins von vielen Beispielen. Das gleiche Ergebnis lässt sich auch mit anderen Methoden erzielen.

Neben vereinfachten Daten ist es auch wichtig, nur durchsuchbare Daten zu pullen. Durchsuchbare Daten sind alphanumerisch. Cognitive Search kann unabhängig vom Format keine Binärdaten durchsuchen, aber Textbeschreibungen aus Bilddateien extrahieren und ableiten (siehe [KI-Erweiterung](cognitive-search-concept-intro.md)), um durchsuchbare Inhalte zu erstellen. Außerdem können mithilfe der KI-Anreicherung umfangreiche Texte durch Modelle für natürliche Sprache analysiert werden, um die Struktur oder relevante Informationen zu ermitteln und neue Inhalte zu erstellen, die Sie einem Suchdokument hinzufügen können.

Da durch Indexer keine Datenprobleme behoben werden, sind ggf. andere Arten der Datenbereinigung oder -bearbeitung erforderlich. Weitere Informationen finden Sie in der Produktdokumentation Ihres [Azure-Datenbankprodukts](/azure/?product=databases).

## <a name="know-your-index"></a>Verstehen Ihres Index

Wie bereits erwähnt, übergeben Indexer die Suchdokumente zur Indizierung an die Suchmaschine. Doch nicht nur Indexer verfügen über Eigenschaften zur Steuerung des Ausführungsverhaltens, auch ein Indexschema besitzt Eigenschaften, die sich grundlegend auf die Indizierung von Zeichenfolgen auswirken. (Nur Zeichenfolgen werden analysiert und mit Token versehen.) Die indizierten Zeichenfolgen können sich je nach Analysezuweisungen von den übergebenen Daten unterscheiden. Die Auswirkungen von Analysetools können mithilfe von [Text analysieren (Azure Cognitive Search-REST-API)](/rest/api/searchservice/test-analyzer) ausgewertet werden. Weitere Informationen zu Analysetools finden Sie unter [Analysetools für Textverarbeitung in der kognitiven Azure-Suche](search-analyzers.md).

Die Interaktion von Indexern mit einem Index beschränkt sich auf die Überprüfung von Feldnamen und -typen. Es gibt keinen Validierungsschritt, um die Richtigkeit des eingehenden Inhalts für das entsprechende Suchfeld im Index zu überprüfen. Als Überprüfungsschritt können Sie Abfragen für den aufgefüllten Index ausführen, die gesamte Dokumente oder ausgewählte Felder zurückgeben. Weitere Informationen zum Abfragen des Inhalts eines Index finden Sie unter [Erstellen einer Abfrage in Azure Cognitive Search](search-query-create.md).

## <a name="next-steps"></a>Nächste Schritte

+ [Zeitplanindexer](search-howto-schedule-indexers.md)
+ [Definieren von Feldzuordnungen](search-indexer-field-mappings.md)
+ [Überwachen des Status und der Ergebnisse von Indexern der kognitiven Azure-Suche](search-howto-monitor-indexers.md)
+ [Herstellen einer Verbindung mithilfe von verwalteten Identitäten](search-howto-managed-identities-data-sources.md)