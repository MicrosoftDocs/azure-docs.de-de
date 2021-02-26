---
title: Durchsuchen von Nur-Text-Blobs
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie einen Suchindexer, um Nur-Text aus Azure-Blobs für eine Volltextsuche in Azure Cognitive Search zu extrahieren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509451"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Indizieren von Nur-Text-Blobs in Azure Cognitive Search

Wenn Sie einen [Blob-Indexer](search-howto-indexing-azure-blob-storage.md) verwenden, um durchsuchbaren Blobtext für die Volltextsuche zu extrahieren, können Sie einen Analysemodus zuweisen, um bessere Indizierungsergebnisse zu erzielen. In der Standardeinstellung analysiert der Indexer Blobinhalt als ein einzelnes Textsegment. Wenn jedoch alle Blobs Nur-Text in derselben Codierung enthalten, können Sie die Indizierungsleistung signifikant verbessern, indem Sie den `text`-Analysemodus nutzen.

Empfehlungen für die Verwendung der `text`-Verarbeitung sind:

+ Datei ist eine TXT-Datei.
+ Dateien weisen einen beliebigen Typ auf, aber der Inhalt selbst ist Text (z. B. Programmquellcode, HTML, XML usw.). Bei Dateien in einer Markupsprache werden alle Syntaxzeichen als statischer Text behandelt.

Bedenken Sie, dass alle Indexer nach JSON serialisiert werden. Der Inhalt der gesamten Textdatei wird standardmäßig in einem großen Feld als `"content": "<file-contents>"` indiziert. Alle Neue-Zeile- und Return-Anweisungen werden in das Inhaltsfeld eingebettet und als `\r\n\` ausgedrückt.

Wenn Sie ein präziseres Ergebnis wünschen und wenn der Dateityp kompatibel ist, ziehen Sie die folgenden Lösungen in Erwägung:

+ [`delimitedText`](search-howto-index-csv-blobs.md)-Analysemodus, wenn die Quelle eine CSV-Datei ist
+ [`jsonArray` oder `jsonLines`](search-howto-index-json-blobs.md), wenn die Quelle JSON-Code ist

Eine dritte Option zum Aufbrechen von Inhalt in mehrere Teile erfordert erweiterte Features in Form von [KI-Anreicherung](cognitive-search-concept-intro.md). Es wird eine Analyse hinzugefügt, mit der Teile der Datei identifiziert und verschiedenen Suchfeldern zugeordnet werden. Sie finden möglicherweise eine vollständige oder teilweise Lösung durch [integrierte Skills](cognitive-search-predefined-skills.md), aber eine wahrscheinlichere Lösung wäre ein Lernmodell, das Ihren Inhalt versteht, artikuliert in einem benutzerdefinierten Lernmodell, verpackt in einem [benutzerdefinierten Skill](cognitive-search-custom-skill-interface.md).

## <a name="set-up-plain-text-indexing"></a>Einrichten der Nur-Text-Indizierung

Erstellen oder aktualisieren Sie zum Indizieren von Nur-Text-Blobs eine Indexerdefinition mit der für `text` festgelegten `parsingMode`-Konfigurierungseigenschaft in einer [Indexer erstellen](/rest/api/searchservice/create-indexer)-Anforderung:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

Standardmäßig wird von der `UTF-8`-Codierung ausgegangen. Um eine andere Codierung anzugeben, verwenden Sie die `encoding`-Konfigurationseigenschaft. 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Anforderungsbeispiel

In der Indexerdefinition werden die Definitionsmodi angegeben.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Nächste Schritte

+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Indizieren von Dokumenten in Azure Blob Storage mit der kognitiven Azure-Suche](search-howto-indexing-azure-blob-storage.md)
+ [Hinzufügen der Volltextsuche zu Azure-Blobdaten mithilfe von Azure Cognitive Search](search-blob-storage-integration.md)