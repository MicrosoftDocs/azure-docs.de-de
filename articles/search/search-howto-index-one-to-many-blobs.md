---
title: Indizieren von Blobs mit mehreren Dokumenten
titleSuffix: Azure Cognitive Search
description: Durchforsten Sie Azure-Blobs nach Textinhalten mithilfe des Blobindexers von Azure Cognitive Search, wobei jedes Blob ein einzelnes Suchindexdokument oder mehrere Suchindexdokumente ergeben kann.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430979"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indizieren von Blobs zum Generieren mehrerer Suchdokumente

Standardmäßig behandelt ein Blobindexer die Inhalte eines Blobs als ein Suchdokument. Wenn Sie in einem Suchindex eine differenziertere Darstellung des Blobs benötigen, können Sie Werte vom Typ **parsingMode** festlegen, um mehrere Suchdokumente für ein einzelnes Blob zu erstellen. Zu den Werten vom Typ **parsingMode**, die zahlreiche Suchdokumente ergeben, zählen `delimitedText` (für [CSV](search-howto-index-csv-blobs.md)) und `jsonArray` oder `jsonLines` (für [JSON](search-howto-index-json-blobs.md)).

Bei Verwendung eines dieser Analysemodi müssen die neu generierten Suchdokumente über eindeutige Dokumentschlüssel verfügen, und es gibt ein Problem bei der Bestimmung, woher der Wert stammt. Das übergeordnete Blob verfügt zwar über mindestens einen eindeutigen Wert in Form von `metadata_storage_path property`, wenn dieser Wert jedoch in mehreren Suchdokumenten verwendet wird, ist der Schlüssel im Index nicht mehr eindeutig.

Daher wird vom Blobindexer ein Azure Search-Dokumentschlüssel (`AzureSearch_DocumentKey`) erstellt, durch den jedes untergeordnete Suchdokument, das auf der Grundlage des einzelnen übergeordneten Blobs erstellt wird, eindeutig identifizierbar ist. In diesem Artikel erfahren Sie, wie dieses Feature funktioniert.

## <a name="one-to-many-document-key"></a>1:n-Dokumentschlüssel

Jedes Dokument, das in einem Index der kognitiven Azure-Suche angezeigt wird, wird eindeutig durch einen Dokumentschlüssel identifiziert. 

Ohne Angabe eines Analysemodus und ohne [explizite Feldzuordnung](search-indexer-field-mappings.md) in der Indexerdefinition für den Suchdokumentschlüssel wird automatisch `metadata_storage_path property` als Dokumentschlüssel zugeordnet. Dadurch wird sichergestellt, dass jedes Blob als eindeutiges Suchdokument erkannt wird, und Sie müssen dieses Feld nicht extra selbst erstellen. (Normalerweise werden nur Felder mit identischem Namen und Typ automatisch zugeordnet.)

Wenn einer der oben aufgeführten Analysemodi verwendet wird, wird ein Blob mehreren Suchdokumenten zugeordnet, sodass ein Dokumentschlüssel, der nur auf Blobmetadaten basiert, ungeeignet ist. In der kognitiven Azure-Suche kann jedoch ein 1:n-Dokumentschlüssel für jede einzelne Entität generiert werden, die aus einem Blob extrahiert wird, um diese Einschränkung zu umgehen. Diese Eigenschaft mit dem Namen „AzureSearch_DocumentKey“ wird jeder einzelnen Entität hinzugefügt, die aus dem Blob extrahiert wird. Der Wert dieser Eigenschaft ist für jede einzelne Entität blobübergreifend garantiert eindeutig, und die Entitäten werden als separate Suchdokumente angezeigt.

Wenn keine expliziten Feldzuordnungen für das Schlüsselindexfeld angegeben werden, wird mit der Feldzuordnungsfunktion `base64Encode` standardmäßig `AzureSearch_DocumentKey` zugeordnet.

## <a name="example"></a>Beispiel

Angenommen, Ihre Indexdefinition weist folgende Felder auf:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Und Ihr Blobcontainer enthält Blobs mit folgender Struktur:

_Blob1.json_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.json_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

Wenn Sie einen Indexer erstellen und **parsingMode** auf `jsonLines` festlegen, ohne explizite Feldzuordnungen für das Schlüsselfeld anzugeben, wird implizit die folgende Zuordnung angewendet.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Dadurch ergeben sich eindeutige Dokumentschlüssel wie in der folgenden Abbildung zu sehen. (Die Base64-codierte ID wurde hier zur besseren Übersichtlichkeit gekürzt.)

| id | Temperatur | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Benutzerdefinierte Feldzuordnung für das Indexschlüsselfeld

Angenommen, Ihr Blobcontainer enthält Blobs mit folgender Struktur (bei Verwendung der gleichen Indexdefinition wie im vorherigen Beispiel):

_Blob1.json_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.json_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

Wenn Sie einen Indexer mit **parsingMode** `delimitedText` erstellen, kommt es Ihnen möglicherweise natürlich vor, wie folgt eine Feldzuordnungsfunktion für die Schlüsselfelder einzurichten:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

Aus dieser Zuordnung ergeben sich jedoch _nicht_ 4 Dokumente, die im Index angezeigt werden, da das Feld `recordid` nicht _blobübergreifend_ eindeutig ist. Deshalb wird empfohlen, dass Sie die implizite Feldzuordnung verwenden, die von der Eigenschaft `AzureSearch_DocumentKey` auf das Schlüsselindexfeld für 1:n-Analysemodi angewendet wird.

Wenn Sie eine explizite Feldzuordnung einrichten möchten, sollten Sie sicherstellen, dass _sourceField_ für jede einzelne Entität **blobübergreifend** eindeutig ist.

> [!NOTE]
> Der von `AzureSearch_DocumentKey` verwendete Ansatz zur Sicherstellung der Eindeutigkeit jeder extrahierten Entität kann sich ändern, und Sie sollten sich deshalb für die Anforderungen Ihrer Anwendung nicht darauf verlassen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie noch nicht mit der grundlegenden Struktur und dem Workflow der Blobindizierung vertraut sind, sollten Sie zunächst [Indizieren von Azure Blob Storage mit Azure Cognitive Search](search-howto-index-json-blobs.md) lesen. Weitere Informationen zum Analysemodus für die verschiedenen Blobinhaltstypen finden Sie in den folgenden Artikeln.

> [!div class="nextstepaction"]
> [Indizieren von CSV-Blobs](search-howto-index-csv-blobs.md)
> [Indizieren von JSON-Blobs](search-howto-index-json-blobs.md)
