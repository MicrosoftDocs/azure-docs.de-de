---
title: Schreiben von gespeicherten Prozeduren und Triggern mit der JavaScript-Abfrage-API in Azure Cosmos DB
description: Erfahren Sie, wie Sie gespeicherte Prozeduren und Trigger mit der JavaScript-Abfrage-API in Azure Cosmos DB schreiben.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 449af63ef3409b8c8173edef7a5d77074cca02b9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335839"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Schreiben von gespeicherten Prozeduren und Triggern in Azure Cosmos DB mit der JavaScript-Abfrage-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mit Azure Cosmos DB können Sie optimierte Abfragen ausführen, indem Sie eine intuitive JavaScript-Schnittstelle verwenden, ohne die SQL-Sprache kennen zu müssen, die zum Schreiben von gespeicherten Prozeduren oder Triggern verwendet wird. Weitere Informationen zur Unterstützung der JavaScript-Abfrage-API in Azure Cosmos DB finden Sie unter [Arbeiten mit der in die JavaScript-Sprache integrierten Abfrage-API in Azure Cosmos DB](javascript-query-api.md).

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Gespeicherte Prozedur mit der JavaScript-Abfrage-API

Das folgende Codebeispiel zeigt, wie die JavaScript-Abfrage-API im Kontext einer gespeicherten Prozedur verwendet wird. Die gespeicherte Prozedur fügt ein Azure Cosmos-Element ein, das durch einen Eingabeparameter angegeben wird, und aktualisiert mit der `__.filter()`-Methode ein Metadatenelement, wobei „minSize“, „maxSize“ und „totalSize“ auf der size-Eigenschaft des Eingabeelements basieren.

> [!NOTE]
> `__` (doppelter Unterstrich) ist bei Verwendung der JavaScript-Abfrage-API ein Alias für `getContext().getCollection()`.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen in Azure Cosmos DB:

* [Arbeiten mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Registrieren und Verwenden von gespeicherten Prozeduren in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Registrieren und Verwenden von [vorangestellten Triggern](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) und [nachgestellten Triggern](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) in Azure Cosmos DB

* [Registrieren und Verwenden von benutzerdefinierten Funktionen in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Create a synthetic partition key](synthetic-partition-keys.md) (Erstellen eines synthetischen Partitionsschlüssels)
