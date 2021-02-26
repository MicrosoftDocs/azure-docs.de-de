---
title: Durchsuchen von JSON-Blobs
titleSuffix: Azure Cognitive Search
description: Durchforsten von Azure-JSON-Blobs nach Textinhalten mithilfe des Blobindexers der kognitiven Azure-Suche. Indexer automatisieren die Datenerfassung für ausgewählte Datenquellen wie Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259049"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Indizieren von JSON-Blobs mit einem Blobindexer in der kognitiven Azure-Suche

In diesem Artikel wird die [Konfiguration eines Blobindexers](search-howto-indexing-azure-blob-storage.md) für Blobs beschrieben, die aus JSON-Dokumenten bestehen. JSON-Blobs in Azure Blob Storage treten üblicherweise in einer der folgenden Formen auf:

+ Einzelnes JSON-Dokument
+ JSON-Dokument mit einem Array von wohlgeformten JSON-Elementen
+ JSON-Dokument mit mehreren Entitäten, die durch einen Zeilenvorschub getrennt sind

Der Blobindexer enthält einen **`parsingMode`** -Parameter, um die Ausgabe des Suchdokuments basierend auf der Struktur zu optimieren. Die Analysemodi umfassen die folgenden Optionen:

| parsingMode | JSON-Dokument | BESCHREIBUNG |
|--------------|-------------|--------------|
| **`json`** | Ein Dokument pro Blob | (Standard) JSON-Blobs werden als einzelne Textblöcke analysiert. Jedes JSON-Blob wird zu einem einzelnen Suchdokument. |
| **`jsonArray`** | Mehrere Dokumente pro Blob | Analysiert ein JSON-Array im Blob. Jedes Element des Arrays wird zu einem separaten Suchdokument.  |
| **`jsonLines`** | Mehrere Dokumente pro Blob | Analysiert ein Blob, das mehrere JSON-Entitäten (auch ein Array) enthält, wobei die einzelnen Entitäten durch einen Zeilenvorschub getrennt sind. Der Indexer startet nach jeder neuen Zeile ein neues Suchdokument. |

Für **`jsonArray`** und **`jsonLines`** finden Sie unter [Indizieren von Blobs zum Generieren mehrerer Suchdokumente](search-howto-index-one-to-many-blobs.md) Informationen dazu, wie mit dem Blobindexer die Eindeutigkeit des Dokumentschlüssels für mehrere Suchdokumente gehandhabt wird, die aus dem gleichen Blob generiert wurden.

Innerhalb der Indexerdefinition können Sie optional [Feldzuordnungen](search-indexer-field-mappings.md) festlegen, um auszuwählen, welche Eigenschaften des JSON-Quelldokuments zum Auffüllen des Zielsuchindex verwendet werden sollen. Wenn bei Verwendung des **`jsonArray`** -Analysemodus das Array beispielsweise als Eigenschaft auf niedrigerer Ebene vorhanden ist, können Sie eine **`document root`** -Eigenschaft festlegen, mit der angegeben wird, wo das Array im Blob angeordnet ist.

In den folgenden Abschnitten werden die einzelnen Modi ausführlicher beschrieben. Wenn Sie mit Indexerclients und -konzepten nicht vertraut sind, finden Sie entsprechende Informationen unter [Erstellen eines Suchindexers](search-howto-create-indexers.md). Sie sollten außerdem mit den Details der [grundlegenden Konfiguration von Blobindexern](search-howto-indexing-azure-blob-storage.md) vertraut sein, die hier nicht wiederholt wird.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indizieren einzelner JSON-Dokumente (eines pro Blob)

Standardmäßig analysieren Blobindexer JSON-Blobs als einzelne Textblöcke, jeweils ein Suchdokument für jeden Blob in einem Container. Wenn das JSON-Blob strukturiert ist, kann diese Struktur im Suchdokument wiedergegeben werden, wobei einzelne Elemente als einzelne Felder dargestellt werden. Angenommen, Sie verwenden in Azure Blob Storage das folgende JSON-Dokument:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Der Blobindexer löst das JSON-Dokument in ein einzelnes Suchdokument auf und lädt einen Index durch Abgleichen der Elemente „text“, „datePublished“ und „tags“ aus der Quelle mit Zielindexfeldern, die den gleichen Namen und Typ aufweisen. Bei einem Index mit den Feldern „text“, „datePublished“ und „tags“ kann der Blobindexer die richtige Zuordnung ableiten, ohne dass in der Anforderung eine Feldzuordnung enthalten ist.

Obwohl als Standardverhalten ein Suchdokument pro JSON-Blob vorgegeben ist, ändern sich durch Festlegen des „json“-Analysemodus die internen Feldzuordnungen für den Inhalt, wodurch Felder in `content` auf tatsächliche Felder im Suchindex heraufgestuft werden. Eine Indexerdefinition für den **`json`** -Analysemodus kann beispielsweise wie folgt aussehen:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Wie bei allen Indexern sollten Sie bei nicht eindeutig übereinstimmenden Feldern davon ausgehen, dass Sie einzelne [Feldzuordnungen](search-indexer-field-mappings.md) explizit angeben müssen, es sei denn, Sie verwenden die impliziten Feldzuordnungen, die für Blobinhalte und -metadaten verfügbar sind, wie unter der [grundlegenden Konfiguration von Blobindexern](search-howto-indexing-azure-blob-storage.md) beschrieben.

### <a name="json-example-single-hotel-json-files"></a>json-Beispiel (JSON-Dateien für einzelne Hotels)

Das Dataset [hotel-json-documents](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) auf GitHub ist hilfreich beim Testen der JSON-Analyse, wobei jedes Blob eine strukturierte JSON-Datei darstellt. Sie können die Datendateien in Blob Storage hochladen und den **Datenimport-Assistenten** verwenden, um schnell auszuwerten, wie dieser Inhalt in einzelne Suchdokumente aufgelöst wird. 

Das Dataset besteht aus fünf Blobs, die jeweils ein „Hotel“-Dokument mit einer „Adressen“-Sammlung und einer „Zimmer“-Sammlung enthalten. Der Blobindexer erkennt beide Sammlungen und gibt die Struktur der Eingabedokumente im Indexschema wieder.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analysieren von JSON-Arrays

Alternativ können Sie auch die JSON-Arrayoption verwenden. Diese Option ist nützlich, wenn Blobs ein Array von wohlgeformten JSON-Objekten enthalten und jedes Element zu einem separaten Suchdokument werden soll. Bei Verwendung von **`jsonArrays`** werden mit dem folgenden JSON-Blob drei separate Dokumente generiert, jedes jeweils mit den Feldern `"id"` und `"text"`.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

Die **`parameters`** -Eigenschaft des Indexers enthält Werte für den Analysemodus. Für ein JSON-Array sollte die Indexerdefinition dem folgenden Beispiel ähneln.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays-Beispiel (Beispieldaten für klinische Studien)

Das Dataset [clinical-trials-json](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) auf GitHub ist hilfreich beim Testen der Analyse von JSON-Arrays. Sie können die Datendateien in Blob Storage hochladen und den **Datenimport-Assistenten** verwenden, um schnell auszuwerten, wie dieser Inhalt in einzelne Suchdokumente aufgelöst wird. 

Das Dataset besteht aus acht Blobs, die jeweils ein JSON-Array von Entitäten enthalten, mit insgesamt 100 Entitäten. Die Entitäten unterscheiden sich darin, welche Felder gefüllt werden, das Endergebnis ist jedoch ein Suchdokument pro Entität aus allen Arrays in allen Blobs.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Analysieren von geschachtelten JSON-Arrays

Damit JSON-Arrays geschachtelte Elemente enthalten, können Sie ein **`documentRoot`** -Element angeben, um eine Struktur mit mehreren Ebenen anzugeben. Angenommen, Ihre Blobs sehen folgendermaßen aus:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Verwenden Sie diese Konfiguration, um das in der `level2`-Eigenschaft enthaltene Array zu indizieren:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Analysieren von durch Zeilenvorschübe getrennten JSON-Entitäten

Wenn das Blob mehrere JSON-Entitäten enthält, die durch einen Zeilenvorschub getrennt sind, und jedes Element ein separates Suchdokument werden soll, verwenden Sie **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Für JSON-Zeilen sollte die Indexerdefinition dem folgenden Beispiel ähneln.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines-Beispiel (Beispieldaten für Fallrechtsammlung)

Das Dataset [caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) auf GitHub ist hilfreich beim Testen der Analyse von JSON-Entitäten, die durch Zeilenvorschübe getrennt sind. Wie bei den anderen Beispieldaten können Sie diese Daten in Blob Storage hochladen und den **Datenimport-Assistenten** verwenden, um schnell die Auswirkungen des Analysemodus auf einzelne Blobs auszuwerten.

Das Dataset besteht aus einem Blob mit 10 JSON-Entitäten, die durch einen Zeilenvorschub getrennt sind, wobei jede Entität einen einzelnen Rechtsfall beschreibt. Das Endergebnis ist ein Suchdokument pro Entität.

## <a name="map-json-fields-to-search-fields"></a>Zuordnen von JSON-Feldern zu Suchfeldern

Mit Feldzuordnungen wird in den Fällen, in denen die Feldnamen und Feldtypen nicht identisch sind, ein Quellfeld einem Zielfeld zugeordnet. Feldzuordnungen können außerdem verwendet werden, um Teile eines JSON-Dokuments abzugleichen und auf Felder der obersten Ebene des Suchdokuments „hochzustufen“.

Dies wird im folgenden Beispiel veranschaulicht. Weitere allgemeine Informationen zu Feldzuordnungen finden Sie unter [Feldzuordnungen](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Angenommen, Sie haben einen Suchindex mit den folgenden Feldern: `text` vom Typ `Edm.String`, `date` vom Typ `Edm.DateTimeOffset` und `tags` vom Typ `Collection(Edm.String)`. Beachten Sie die Abweichung zwischen „datePublished“ in der Quelle und dem Feld `date` im Index. Um das JSON-Objekt in der gewünschten Form zuzuordnen, verwenden Sie die folgenden Feldzuordnungen:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Quellfelder werden mit der [JSON-Zeiger](https://tools.ietf.org/html/rfc6901)-Notation angegeben. Sie beginnen mit einem Schrägstrich, um auf das Stammverzeichnis des JSON-Dokuments zu verweisen, und wählen dann mittels eines schrägstrichgetrennten Weiterleitungspfads den Pfad zur gewünschten Eigenschaft (auf beliebiger Schachtelungsebene) aus.

Sie können auch mit einem nullbasierten Index auf einzelne Arrayelemente verweisen. Um z. B. das erste Element des Arrays „tags“ aus dem obigen Beispiel auszuwählen, verwenden Sie eine Feldzuordnung wie folgt:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Wenn **`sourceFieldName`** auf eine Eigenschaft verweist, die im JSON-Blob nicht vorhanden ist, wird diese Zuordnung ohne Fehler übersprungen. Dieses Verhalten ermöglicht das Fortsetzen der Indizierung für JSON-Blobs, die ein anderes Schema haben (ein häufiger Anwendungsfall). Da keine Validierungsüberprüfung durchgeführt wird, sollten Sie die Zuordnungen sorgfältig auf Tippfehler prüfen, damit keine Dokumente verloren gehen.
>

## <a name="next-steps"></a>Nächste Schritte

+ [Konfigurieren von Blobindexern](search-howto-indexing-azure-blob-storage.md)
+ [Definieren von Feldzuordnungen](search-indexer-field-mappings.md)
+ [Indexer in Azure Search](search-indexer-overview.md)
+ [Indizieren von CSV-Blobs mit einem Blobindexer](search-howto-index-csv-blobs.md)
+ [Tutorial: Durchsuchen von teilweise strukturierten Daten in Azure Blob Storage](search-semi-structured-data.md)