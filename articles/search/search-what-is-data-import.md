---
title: Import und Datenerfassung in Suchindizes
titleSuffix: Azure Cognitive Search
description: Füllen Sie Daten aus externen Datenquellen auf, und laden Sie sie in einen Index der kognitiven Azure-Suche hoch.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: b57d55e91918ba612ad42acd5e6059ae0dbd0090
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422449"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Übersicht über den Datenimport – kognitive Azure-Suche

Bei der kognitiven Azure-Suche werden Abfragen für Inhalte ausgeführt, die in einen [Suchindex](search-what-is-an-index.md) geladen und dort gespeichert wurden. In diesem Artikel werden die zwei grundlegenden Ansätze zum Auffüllen eines Index untersucht: Sie können die Daten programmgesteuert in den Index *pushen* , oder Sie können für einen [Indexer der kognitiven Azure-Suche](search-indexer-overview.md) auf eine unterstützte Datenquelle verweisen, um die Daten zu *pullen*.

Bei beiden Methoden besteht das Ziel darin, Daten aus einer externen Datenquelle in einen Azure Cognitive Search-Index zu laden. Die kognitive Azure-Suche ermöglicht Ihnen das Erstellen eines leeren Index, dieser kann aber erst abgefragt werden, wenn Sie Daten in ihn pushen oder pullen.

> [!NOTE]
> Wenn die Lösung [KI-Anreicherung](cognitive-search-concept-intro.md) erfordert, muss zum Laden eines Index das Pullmodell (Indexer) verwendet werden. Eine externe Verarbeitung wird nur über an einen Indexer angefügte Skillsets unterstützt.

## <a name="pushing-data-to-an-index"></a>Übertragen von Daten per Pushvorgang in einen Index

Das Pushmodell, das zum programmgesteuerten Senden der Daten an die kognitive Azure-Suche verwendet wird, ist der flexibelste Ansatz. Erstens gelten keine Einschränkungen für den Datenquellentyp. Alle Datasets, die aus JSON-Dokumenten bestehen, können in einen Index der kognitiven Azure-Suche gepusht werden, sofern jedes Dokument im Dataset über Felder verfügt, die den im Indexschema definierten Feldern zugeordnet sind. Zweitens gelten keine Einschränkungen für die Ausführungshäufigkeit. Sie können Änderungen beliebig oft per Pushvorgang in einen Index übertragen. Bei Anwendungen mit sehr niedrigen Latenzanforderungen (wenn z.B. Suchvorgänge mit dynamischen Inventardatenbanken synchronisiert werden müssen) ist das Push-Modell Ihre einzige Option.

Dieser Ansatz ist flexibler als ein Pull-Modell, da Sie Dokumente einzeln oder in Batches hochladen können (bis zu 1000 pro Batch oder 16 MB, je nachdem, welches Limit zuerst erreicht wird). Das Pushmodell ermöglicht Ihnen auch das Hochladen von Dokumenten in die kognitive Azure-Suche, unabhängig davon, wo sich die Daten befinden.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Pushen von Daten in einen Index der kognitiven Azure-Suche

Sie können die folgenden APIs verwenden, um ein oder mehrere Dokumente in einen Index zu laden:

+ [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (REST-API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction-Klasse](/dotnet/api/azure.search.documents.models.indexdocumentsaction) oder [IndexDocumentsBatch-Klasse](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

Es gibt derzeit keine Toolunterstützung für die Push-Übertragung von Daten über das Portal.

Eine Einführung in jede Methodik finden Sie unter [Schnellstart: Erstellen eines Index der kognitiven Azure-Suche mit PowerShell](./search-get-started-powershell.md) oder [C#-Schnellstart: Erstellen eines Index der kognitiven Azure-Suche mit dem .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indizierungsaktionen: upload, merge, mergeOrUpload, delete

Sie können den Typ der Indizierungsaktion dokumentweise steuern, indem Sie angeben, ob das Dokument vollständig hochgeladen, mit vorhandenen Dokumentinhalten zusammengeführt oder gelöscht werden soll.

Bei der REST-API werden HTTP POST-Anforderungen mit JSON-Anforderungstexten an die Endpunkt-URL des Index der kognitiven Azure-Suche ausgegeben. Jedes JSON-Objekt im value-Array enthält den Schlüssel des Dokuments und gibt an, ob mit einer Indizierungsaktion Dokumentinhalt hinzugefügt, aktualisiert oder gelöscht wird. Ein Codebeispiel finden Sie unter [Laden von Dokumenten](search-get-started-dotnet.md#load-documents).

Beim .NET SDK packen Sie Ihre Daten in einem `IndexBatch`-Objekt. Ein `IndexBatch`-Objekt kapselt eine Sammlung mit `IndexAction`-Objekten. Jedes Objekt enthält ein Dokument und eine Eigenschaft, die der kognitiven Azure-Suche mitteilt, welche Aktion für das jeweilige Dokument durchgeführt werden soll. Ein Codebeispiel finden Sie unter [C#-Schnellstart](search-get-started-dotnet.md).


| @search.action | BESCHREIBUNG | Erforderliche Felder für jedes Dokument | Notizen |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Eine `upload` -Aktion entspricht „upsert“, wobei neue Dokumente eingefügt und bestehende Dokumente aktualisiert/ersetzt werden. |Schlüssel und alle anderen zu definierenden Felder |Wenn ein bestehendes Dokument aktualisiert/ersetzt wird, werden alle in der Anforderung nicht festgelegten Felder auf `null`festgelegt. Dies tritt auch auf, wenn das Feld zuvor auf einen Wert festgelegt wurde, der nicht Null ist. |
| `merge` |Aktualisiert ein bestehendes Dokument mit den angegebenen Feldern. Wenn das Dokument im Index nicht vorhanden ist, schlägt die Zusammenführung fehl. |Schlüssel und alle anderen zu definierenden Felder |Jedes Feld, das Sie in einer Zusammenführung angeben, ersetzt das vorhandene Feld im Dokument. Beim .NET SDK schließt dies auch Felder vom Typ `DataType.Collection(DataType.String)` ein. Bei der REST-API schließt dies auch Felder vom Typ `Collection(Edm.String)` ein. Wenn das Dokument beispielsweise das Feld `tags` mit dem Wert `["budget"]` enthält und Sie eine Zusammenführung mit dem Wert `["economy", "pool"]` für `tags` durchführen, hat das Feld `tags` am Ende den Wert `["economy", "pool"]`. Der Wert lautet nicht `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Diese Aktion verhält sich wie `merge`, wenn im Index bereits ein Dokument mit dem entsprechenden Schlüssel vorhanden ist. Wenn das Dokument nicht vorhanden ist, verhält es sich wie `upload` mit einem neuen Dokument. |Schlüssel und alle anderen zu definierenden Felder |- |
| `delete` |Hiermit wird das angegebene Dokument aus dem Index gelöscht. |Nur Schlüssel |Mit Ausnahme des Schlüsselfelds werden alle angegebenen Felder ignoriert. Wenn Sie ein einzelnes Feld aus einem Dokument entfernen möchten, verwenden Sie stattdessen `merge` , und setzen Sie das Feld ausdrücklich auf Null. |

### <a name="formulate-your-query"></a>Formulieren der Abfrage

Es gibt zwei Möglichkeiten, um [den Index mithilfe der REST-API zu durchsuchen](/rest/api/searchservice/Search-Documents). Eine Möglichkeit besteht darin, eine HTTP POST-Anforderung auszugeben, wobei die Abfrageparameter in ein JSON-Objekt im Anforderungstext definiert werden. Die andere Möglichkeit besteht darin, eine HTTP GET-Anforderung auszugeben, wobei die Abfrageparameter in der Anforderungs-URL definiert werden. Die Beschränkungen in Bezug auf die Größe der Abfrageparameter sind bei POST [geringer](/rest/api/searchservice/Search-Documents) als bei GET. Aus diesem Grund empfehlen wir die Verwendung von POST, sofern GET nicht aufgrund bestimmter Umstände praktischer wäre.

Sowohl bei POST- als auch bei GET-Vorgängen müssen in der Anforderungs-URL der *Dienstname* , der *Indexname* und eine *API-Version* angegeben werden. 

Für GET befindet sich die *Abfragezeichenfolge* am Ende der URL, wo Sie die Abfrageparameter angeben. Das URL-Format finden Sie weiter unten:

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

Das Format für POST ist das Gleiche, allerdings mit `api-version` in den Parametern für die Abfragezeichenfolge.

## <a name="pulling-data-into-an-index"></a>Übertragen von Daten per Pullvorgang in einen Index

Beim Pull-Modell wird eine unterstützte Datenquelle durchsucht, und die Daten werden für Sie automatisch in Ihren Index hochgeladen. In der kognitiven Azure-Suche wird diese Funktion mit *Indexern* implementiert, die derzeit für folgende Plattformen verfügbar sind:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Tabellenspeicherung](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL-Datenbank, SQL Managed Instance und SQL Server auf virtuellen Azure-Computern](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Mit Indexern wird für einen Index eine Verbindung mit einer Datenquelle (normalerweise eine Tabelle, Sicht oder ähnliche Struktur) hergestellt, und Quellfelder werden gleichwertigen Feldern im Index zugeordnet. Während der Ausführung wird das Rowset automatisch in JSON transformiert und in den angegebenen Index geladen. Alle Indexer unterstützen die Verwendung von Zeitplänen, sodass Sie angeben können, wie häufig die Daten aktualisiert werden sollen. Die meisten Indexer verfügen über eine Änderungsnachverfolgung, wenn dies von der Datenquelle unterstützt wird. Da Änderungen und Löschvorgänge in vorhandenen Dokumenten nachverfolgt und neue Dokumente erkannt werden, ist es mithilfe von Indexern nicht mehr erforderlich, die Daten in Ihrem Index aktiv zu verwalten.

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Pullen von Daten in einen Index der kognitiven Azure-Suche

Die Indexer-Funktion steht im [Azure-Portal](search-import-data-portal.md) und für die [REST-API](/rest/api/searchservice/Indexer-operations) und das [.NET SDK](/dotnet/api/azure.search.documents.indexes.searchindexerclient) zur Verfügung.

Ein Vorteil der Verwendung des Portals ist, dass bei der kognitiven Azure-Suche normalerweise ein Indexstandardschema generiert werden kann, indem die Metadaten des Quelldatasets gelesen werden. Sie können den generierten Index ändern, bis er verarbeitet wird. Nach der Verarbeitung sind nur noch Schemabearbeitungen zulässig, bei denen keine erneute Indizierung erforderlich ist. Wenn die gewünschten Änderungen eine direkte Auswirkung auf das Schema haben, müssen Sie den Index neu erstellen. 

## <a name="verify-data-import-with-search-explorer"></a>Überprüfen des Datenimports mit dem Suchexplorer

Eine schnelle Möglichkeit zum Durchführen einer vorläufigen Prüfung für den Dokumentupload ist die Verwendung des **Suchexplorers** im Portal. Mit dem Explorer können Sie einen Index abfragen, ohne dass Sie dafür Code schreiben müssen. Die Suche basiert auf den Standardeinstellungen, z.B. der [einfachen Syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) und dem [searchMode-Abfrageparameter](/rest/api/searchservice/search-documents), der standardmäßig verwendet wird. Ergebnisse werden im JSON-Format zurückgegeben, damit Sie das gesamte Dokument untersuchen können.

> [!TIP]
> Viele [Codebeispiele der kognitiven Azure-Suche](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) enthalten eingebettete oder sofort verfügbare Datasets, damit Sie schnell beginnen können. Außerdem enthält das Portal auch einen Beispielindexer und eine Datenquelle mit einem kleinen Immobiliendataset („realestate-us-sample“). Wenn Sie den vorkonfigurierten Indexer in der Beispieldatenquelle ausführen, wird ein Index erstellt und mit Dokumenten geladen, die dann im Suchexplorer oder mit von Ihnen geschriebenem Code abgefragt werden können.

## <a name="see-also"></a>Weitere Informationen

+ [Indexer (Übersicht)](search-indexer-overview.md)
+ [Tutorial: Erstellen Ihres ersten Azure Search-Index im Portal](search-get-started-portal.md)