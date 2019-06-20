---
title: Aktualisieren auf die aktuelle Version der REST-API für den Azure-Suchdienst – Azure Search
description: Überprüfen Sie die Unterschiede in den API-Versionen, und erfahren Sie, welche Aktionen erforderlich sind, um vorhandenen Code zur neuesten Version der REST-API für den Azure-Suchdienst zu migrieren.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 85a8ddf4ce87d7ac8ce460c0aff56311a2ea4578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540692"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Aktualisieren auf die aktuelle Version der REST-API für den Azure-Suchdienst
Wenn Sie eine frühere Version der [REST-API für den Azure Search-Dienst](https://docs.microsoft.com/rest/api/searchservice/) verwenden, unterstützt dieser Artikel Sie beim Upgrade Ihrer Anwendung auf die neueste allgemein verfügbare API-Version 2019-05-06.

Version 2019-05-06 der REST-API enthält einige Änderungen im Vergleich zu früheren Versionen. Sie sind größtenteils abwärtskompatibel, daher sollte das Ändern von Code nur minimalen Aufwand bedeuten, abhängig davon, welche Version Sie zuvor verwendet haben. Unter [Schritte zum Upgrade](#UpgradeSteps) sind die erforderlichen Änderungen am Code zur Verwendung der neuen Funktionen beschrieben.

> [!NOTE]
> Eine Azure Search-Dienstinstanz unterstützt eine Reihe von REST-API-Versionen, einschließlich früherer Versionen. Sie können diese API-Versionen weiterhin verwenden, es wird jedoch empfohlen, den Code zur neuesten Version zu migrieren, damit Sie auf neue Funktionen zugreifen können.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Neuerungen in Version 2019-05-06
Version 2019-05-06 ist das neueste allgemein verfügbare Release der REST-API für den Azure Search-Dienst. Funktionen, die in dieser API-Version nun allgemein verfügbar sind:

* [AutoVervollständigen](index-add-suggesters.md) ist eine Funktion zur Textvervollständigung, mit der ein teilweise eingegebener Begriff vervollständigt wird.

* [Komplexe Typen](search-howto-complex-data-types.md) bieten native Unterstützung für strukturierte Objektdaten in einem Azure Search-Index.

* Der [Analysemodus JsonLines](search-howto-index-json-blobs.md) ist Teil der Azure-Blobindizierung. Dabei wird ein Suchdokument pro JSON-Entität erstellt, die durch eine neue Zeile getrennt ist.

* Die [kognitive Suche](cognitive-search-concept-intro.md) ermöglicht eine Indizierung, bei der die Engines zur KI-Anreicherung von Cognitive Services genutzt werden.

Mit diesem allgemein verfügbaren Update fallen mehrere Previewfunktionsreleases zusammen. Die Liste der neuen Previewfunktionen finden Sie unter [REST-API für den Azure Search-Dienst: Version 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Wichtige Änderungen

Vorhandener Code, der die folgenden Funktionen enthält, löst in API-Version 2019-05-06 Fehler aus.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer für Azure Cosmos DB: Datenquelle ist jetzt „type“: „cosmosdb“

Wenn Sie einen [Cosmos DB-Indexer](search-howto-index-cosmosdb.md ) verwenden, müssen Sie `"type": "documentdb"` in `"type": "cosmosdb"` ändern.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Fehler in Indexer-Ausführungsergebnissen weisen keinen Status mehr auf

Die Fehlerstruktur für die Indexer-Ausführung enthielt bisher ein `status`-Element. Dieses Element wurde entfernt, da es keine nützlichen Informationen enthielt.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Die Datenquellen-API des Indexers gibt keine Verbindungszeichenfolgen mehr zurück

Ab den API-Versionen 2019-05-06 und 2019-05-06-Preview gibt die Datenquellen-API als Antwort auf einen REST-Vorgang keine Verbindungszeichenfolgen mehr zurück. In früheren API-Versionen wurde in Azure Search für mithilfe der POST-Methode erstellte Datenquellen **201** zurückgegeben, gefolgt von der OData-Antwort, die die Verbindungszeichenfolge im Textformat enthielt.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Kognitive Qualifikation „Benannte Entität erkennen“ wird nicht mehr verwendet

Wenn Sie die Qualifikation [Benannte Entität erkennen](cognitive-search-skill-named-entity-recognition.md) im Code aufrufen, treten beim Aufruf Fehler auf. Die Ersatzfunktion ist [Entitätserkennung](cognitive-search-skill-entity-recognition.md). Die Referenz der Qualifikation sollte sich ohne weitere Änderungen ersetzen lassen. Die API-Signatur ist für beide Versionen identisch. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Schritte zum Upgrade
Wenn Sie das Upgrade von einer früheren allgemein verfügbaren Version, 2017-11-11 oder 2016-09-01, ausführen, müssen Sie wahrscheinlich keine Änderungen am Code vornehmen, außer die Versionsnummer zu ändern. Nur in folgenden Situationen müssen Sie möglicherweise Code ändern:

* Der Code erzeugt Fehler, wenn nicht erkannte Eigenschaften in einer API-Antwort zurückgegeben werden. Standardmäßig sollte die Anwendung Eigenschaften ignorieren, die sie nicht versteht.

* Ihr Code behält API-Anforderungen bei und versucht, sie erneut an die neue API-Version zu senden. Dies kann beispielsweise vorkommen, wenn Ihre Anwendung Fortsetzungstoken beibehält, die von der Search-API zurückgegeben wurden (weitere Informationen finden Sie, indem Sie in der [Search-API-Referenz](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) nach `@search.nextPageParameters` suchen).

Wenn diese Situationen für Sie zutreffen, müssen Sie den Code möglicherweise entsprechend ändern. Darüber hinaus sollten keine Änderungen nötig sein, es sei denn, Sie möchten die [neuen Funktionen](#WhatsNew) der Version 2019-05-06 verwenden.

Wenn Sie das Upgrade von einer API-Vorschauversion ausführen, gilt das oben Genannte ebenfalls. Sie müssen jedoch außerdem beachten, dass einige Previewfunktionen in Version 2019-05-06 nicht verfügbar sind:

* [MLT-Abfragen (More Like This)](search-more-like-this.md)
* [CSV-Blob-Indizierung](search-howto-index-csv-blobs.md)
* [MongoDB-API-Unterstützung für Cosmos DB-Indexer](search-howto-index-cosmosdb.md)

Wenn diese Funktionen im Code verwendet werden, können Sie erst ein Upgrade auf API-Version 2019-05-06 durchführen, wenn Sie sie nicht mehr nutzen.

> [!IMPORTANT]
> Vorschau-APIs sind für Tests und Evaluierungen bestimmt und sollten nicht in Produktionsumgebungen eingesetzt werden.
> 

### <a name="upgrading-complex-types"></a>Aktualisieren von komplexen Typen

Wenn im Code komplexe Typen mit den älteren API-Vorschauversionen 2017-11-11-Preview oder 2016-09-01-Preview verwendet werden, gelten in Version 2019-05-06 einige neue und geänderte Einschränkungen, die Sie berücksichtigen müssen:

+ Die Limits für die Tiefe der Unterfelder und die Anzahl der komplexen Sammlungen pro Index wurden gesenkt. Wenn Sie mit den API-Vorschauversionen Indizes erstellt haben, die diese Limits überschreiten, treten beim Aktualisieren oder Neuerstellen der Indizes mit API-Version 2019-05-06 Fehler auf. Wenn dies bei Ihnen der Fall ist, müssen Sie Ihr Schema entsprechend den neuen Limits ändern und dann den Index neu erstellen.

+ API-Version 2019-05-06 enthält ein neues Limit für die Anzahl der Elemente von komplexen Sammlungen pro Dokument. Wenn Sie mit den API-Vorschauversionen Indizes mit Dokumenten erstellt haben, die diese Limits überschreiten, treten beim Neuindizieren dieser Daten mit API-Version 2019-05-06 Fehler auf. Wenn dies bei Ihnen der Fall ist, müssen Sie die Anzahl der Elemente von komplexen Sammlungen pro Dokument verringern, bevor Sie die Daten neu indizieren.

Weitere Informationen finden Sie unter [Grenzwerte für den Azure Search-Dienst](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Aktualisieren einer älteren Struktur mit komplexen Typen

Wenn im Code komplexe Typen mit einer der älteren API-Vorschauversionen verwendet werden, verwenden Sie möglicherweise ein Indexdefinitionsformat, das wie folgt aussieht:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

In API-Version 2017-11-11-Preview wurde ein neueres strukturähnliches Format zum Definieren von Indexfeldern eingeführt. In dem neuen Formular enthält jedes komplexe Feld eine Feldsammlung, in der die Unterfelder definiert sind. In API-Version 2019-05-06 wird ausschließlich dieses neue Format verwendet, sodass beim Erstellen oder Aktualisieren eines Index mit dem alten Format Fehler auftreten. Wenn Sie Indizes mit dem alten Format erstellt haben, müssen Sie sie mithilfe der API-Version 2017-11-11-Preview in das neue Format ändern, damit sie mit API-Version 2019-05-06 verwaltet werden können.

Mit den folgenden Schritten können Sie „flache“ Indizes mithilfe der API-Version 2017-11-11-Preview in das neue Format ändern:

1. Führen Sie eine GET-Anforderung aus, um den Index abzurufen. Wenn dieser bereits im neuen Format vorliegt, ist kein weiterer Schritt erforderlich.

2. Wandeln Sie den Index vom „flachen“ Format in das neue Format um. Dafür müssen Sie Code schreiben, da zum Zeitpunkt der Erstellung dieses Artikels kein Beispielcode vorhanden ist.

3. Führen Sie eine PUT-Anforderung aus, um den Index auf das neue Format zu aktualisieren. Stellen Sie sicher, dass keine anderen Details des Index geändert werden, z. B. die Durchsuchbarkeit oder Filterfunktion von Feldern, da dies mit der API zum Aktualisieren von Indizes nicht zulässig ist.

> [!NOTE]
> Indizes, die mit dem alten „flachen“ Format erstellt wurden, können über das Azure-Portal nicht verwaltet werden. Ändern Sie Ihre Indizes schnellstmöglich von der „flachen“ Darstellung in die Darstellung in „Strukturform“.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die Referenzdokumentation für die REST-API für den Azure Search-Dienst an. Wenn Probleme auftreten, können Sie sich über [Stack Overflow](https://stackoverflow.com/) an uns wenden oder [den Support kontaktieren](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referenz zur REST-API für den Azure Search-Dienst](https://docs.microsoft.com/rest/api/searchservice/)

