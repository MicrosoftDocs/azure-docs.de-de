---
title: Durchsuchen von CSV-Blobs
titleSuffix: Azure Cognitive Search
description: Extrahieren und importieren Sie mithilfe des delimitedText-Analysemodus CSV-Dateien aus Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430478"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Indizieren von CSV-Blobs mit dem delimitedText-Analysemodus und mit Blobindexern in Azure Cognitive Search

Der [Blobindexer](search-howto-indexing-azure-blob-storage.md) in Azure Cognitive Search umfasst einen `delimitedText`-Analysemodus für CSV-Dateien, bei dem jede Zeile in einer CSV-Datei als separates Suchdokument verarbeitet wird. Bei dem folgenden durch Trennzeichen getrennten Text ergeben sich mit `delimitedText` z. B. zwei Dokumente im Suchindex: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Ohne den `delimitedText`-Analysemodus wird der gesamte Inhalt der CSV-Datei als ein Suchdokument behandelt.

Wenn Sie mehrere Suchdokumente aus einem einzelnen Blob erstellen, lesen Sie [Indizieren von Blobs zum Generieren mehrerer Suchdokumente](search-howto-index-one-to-many-blobs.md), um zu verstehen, wie die Zuweisung von Dokumentschlüsseln funktioniert. Der Blobindexer kann Werte suchen oder generieren, die jedes neue Dokument eindeutig definieren. Insbesondere kann ein vorübergehendes `AzureSearch_DocumentKey`-Element erstellt werden, das beim Analysieren eines Blobs in kleinere Teile aufgeteilt wird, wobei der Wert dann als Schlüssel des Suchdokuments im Index verwendet wird.

## <a name="setting-up-csv-indexing"></a>Einrichten der CSV-Indizierung

Erstellen oder aktualisieren Sie zum Indizieren von CSV-Blobs eine Indexerdefinition mit dem `delimitedText`-Analysemodus in einer [Indexer erstellen](/rest/api/searchservice/create-indexer)-Anforderung:

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` gibt an, dass die erste (nicht leere) Zeile der einzelnen Blobs Header enthält.
Wenn Blobs am Anfang keine Headerzeile enthalten, sollten die Header in der Indexerkonfiguration angegeben werden: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Sie können das Trennzeichen mithilfe der Konfigurationseinstellung `delimitedTextDelimiter` anpassen. Beispiel:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Derzeit wird nur die UTF-8-Codierung unterstützt. Wenn Sie Unterstützung für andere Codierungen benötigen, stimmen Sie unter [UserVoice](https://feedback.azure.com/forums/263029-azure-search) dafür ab.

> [!IMPORTANT]
> Bei Verwendung des Analysemodus für durch Trennzeichen getrennten Text wird in der kognitiven Azure-Suche davon ausgegangen, dass alle Blobs in der Datenquelle CSV-Blobs sind. Wenn Sie eine Mischung aus CSV- und Nicht-CSV-Blobs in der gleichen Datenquelle unterstützen müssen, stimmen Sie unter [UserVoice](https://feedback.azure.com/forums/263029-azure-search) dafür ab.
>

## <a name="request-examples"></a>Beispiele für Anforderungen

Dies alles wird an vollständigen Nutzlastbeispielen demonstriert. 

Datenquelle: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexer:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


