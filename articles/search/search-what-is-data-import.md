---
title: Importieren von Daten für die Datenerfassung in einem Suchindex – Azure Search
description: Füllen Sie Daten aus externen Datenquellen auf, und laden Sie sie in einen Index in Azure Search hoch.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 83ca0c11ab0065929d939b7345cbd15869740bb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024357"
---
# <a name="data-import-overview---azure-search"></a>Übersicht über den Datenimport – Azure Search

In Azure Search werden Abfragen für Ihre Inhalte ausgeführt, die in einen [Suchindex](search-what-is-an-index.md) geladen und dort gespeichert wurden. In diesem Artikel werden die zwei grundlegenden Ansätze zum Auffüllen eines Indexes untersucht: Sie können Ihre Daten per *Pushvorgang* programmgesteuert in den Index übertragen, oder Sie können für einen [Azure Search-Indexer](search-indexer-overview.md) auf eine unterstützte Datenquelle verweisen, um die Daten per *Pullvorgang* zu beziehen.

Bei beiden Methoden ist das Ziel das *Laden von Daten* aus einer externen Datenquelle in einen Azure Search-Index. Azure Search ermöglicht Ihnen das Erstellen eines leeren Index, dieser kann aber erst abgefragt werden, wenn Sie Daten in ihn pushen oder pullen.

## <a name="pushing-data-to-an-index"></a>Übertragen von Daten per Pushvorgang in einen Index
Das Pushmodell, das zum programmgesteuerten Senden Ihrer Daten an Azure Search verwendet wird, ist der flexibelste Ansatz. Erstens gelten keine Einschränkungen für den Datenquellentyp. Alle Datasets, die aus JSON-Dokumenten bestehen, können per Pushvorgang in einen Azure Search-Index übertragen werden, solange jedes Dokument im Dataset über Felder verfügt, die den in Ihrem Indexschema definierten Feldern zugeordnet sind. Zweitens gelten keine Einschränkungen für die Ausführungshäufigkeit. Sie können Änderungen beliebig oft per Pushvorgang in einen Index übertragen. Bei Anwendungen mit sehr niedrigen Latenzanforderungen (wenn z.B. Suchvorgänge mit dynamischen Inventardatenbanken synchronisiert werden müssen) ist das Push-Modell Ihre einzige Option.

Dieser Ansatz ist flexibler als ein Pull-Modell, da Sie Dokumente einzeln oder in Batches hochladen können (bis zu 1000 pro Batch oder 16 MB, je nachdem, welches Limit zuerst erreicht wird). Das Push-Modell ermöglicht Ihnen auch das Hochladen von Dokumenten in Azure Search, unabhängig davon, wo sich Ihre Daten befinden.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Übertragen von Daten in einen Azure Search-Index per Pushvorgang

Sie können die folgenden APIs verwenden, um ein oder mehrere Dokumente in einen Index zu laden:

+ [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (REST-API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-Klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) oder [indexBatch-Klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Es gibt derzeit keine Toolunterstützung für die Push-Übertragung von Daten über das Portal.

Eine Einführung in jede Methodik finden Sie unter [Schnellstart: Erstellen eines Azure Search-Index mithilfe von PowerShell und der REST-API](search-create-index-rest-api.md) und [Schnellstart: Erstellen eines Azure Search-Index in C#](search-import-data-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indizierungsaktionen: upload, merge, mergeOrUpload, delete

Sie können den Typ der Indizierungsaktion dokumentweise steuern, indem Sie angeben, ob das Dokument vollständig hochgeladen, mit vorhandenen Dokumentinhalten zusammengeführt oder gelöscht werden soll.

Bei der REST-API werden HTTP POST-Anforderungen mit JSON-Anforderungstexten an die Endpunkt-URL Ihres Azure Search-Index ausgegeben. Jedes JSON-Objekt im value-Array enthält den Schlüssel des Dokuments und gibt eine Indizierungsaktion an, die Dokumentinhalt hinzufügt, aktualisiert oder löscht. Ein Codebeispiel finden Sie unter [Laden von Dokumenten](search-create-index-rest-api.md#load-documents).

Beim .NET SDK packen Sie Ihre Daten in einem `IndexBatch`-Objekt. Ein `IndexBatch`-Objekt kapselt eine Sammlung mit `IndexAction`-Objekten. Jedes Objekt enthält ein Dokument und eine Eigenschaft, die Azure Search mitteilt, welche Aktion für das jeweilige Dokument durchgeführt werden soll. Ein Codebeispiel finden Sie unter [Erstellen des IndexBatch-Elements](search-import-data-dotnet.md#construct-indexbatch).


| @search.action | BESCHREIBUNG | Erforderliche Felder für jedes Dokument | Notizen |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Eine `upload` -Aktion entspricht „upsert“, wobei neue Dokumente eingefügt und bestehende Dokumente aktualisiert/ersetzt werden. |Schlüssel und alle anderen zu definierenden Felder |Wenn ein bestehendes Dokument aktualisiert/ersetzt wird, werden alle in der Anforderung nicht festgelegten Felder auf `null`festgelegt. Dies tritt auch auf, wenn das Feld zuvor auf einen Wert festgelegt wurde, der nicht Null ist. |
| `merge` |Aktualisiert ein bestehendes Dokument mit den angegebenen Feldern. Wenn das Dokument im Index nicht vorhanden ist, schlägt die Zusammenführung fehl. |Schlüssel und alle anderen zu definierenden Felder |Jedes Feld, das Sie in einer Zusammenführung angeben, ersetzt das vorhandene Feld im Dokument. Beim .NET SDK schließt dies auch Felder vom Typ `DataType.Collection(DataType.String)` ein. Bei der REST-API schließt dies auch Felder vom Typ `Collection(Edm.String)` ein. Wenn das Dokument beispielsweise das Feld `tags` mit dem Wert `["budget"]` enthält und Sie eine Zusammenführung mit dem Wert `["economy", "pool"]` für `tags` durchführen, hat das Feld `tags` am Ende den Wert `["economy", "pool"]`. Der Wert lautet nicht `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Diese Aktion verhält sich wie `merge`, wenn im Index bereits ein Dokument mit dem entsprechenden Schlüssel vorhanden ist. Wenn das Dokument nicht vorhanden ist, verhält es sich wie `upload` mit einem neuen Dokument. |Schlüssel und alle anderen zu definierenden Felder |- |
| `delete` |Hiermit wird das angegebene Dokument aus dem Index gelöscht. |Nur Schlüssel |Mit Ausnahme des Schlüsselfelds werden alle angegebenen Felder ignoriert. Wenn Sie ein einzelnes Feld aus einem Dokument entfernen möchten, verwenden Sie stattdessen `merge` , und setzen Sie das Feld ausdrücklich auf Null. |

## <a name="decide-which-indexing-action-to-use"></a>Entscheiden Sie, welche Indizierungsaktion verwendet werden soll.
So importieren Sie Daten mit dem .NET SDK (upload, merge, delete und mergeOrUpload). Je nachdem, welche der folgenden Aktionen Sie wählen, müssen für jedes Dokument nur bestimmte Felder eingefügt werden:


### <a name="formulate-your-query"></a>Formulieren der Abfrage
Es gibt zwei Möglichkeiten, um [den Index mithilfe der REST-API zu durchsuchen](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Eine Möglichkeit besteht darin, eine HTTP POST-Anforderung auszugeben, wobei die Abfrageparameter in ein JSON-Objekt im Anforderungstext definiert werden. Die andere Möglichkeit besteht darin, eine HTTP GET-Anforderung auszugeben, wobei die Abfrageparameter in der Anforderungs-URL definiert werden. Die Beschränkungen in Bezug auf die Größe der Abfrageparameter sind bei POST [geringer](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) als bei GET. Aus diesem Grund empfehlen wir die Verwendung von POST, sofern GET nicht aufgrund bestimmter Umstände praktischer wäre.

Sowohl für POST als auch für GET müssen Sie in der Anforderungs-URL Ihren *Dienstnamen*, den *Indexnamen* sowie die entsprechende *API-Version* (die aktuelle Version der API zum Zeitpunkt der Veröffentlichung dieses Dokuments ist `2019-05-06`) bereitstellen. Für GET befindet sich die *Abfragezeichenfolge* am Ende der URL, wo Sie die Abfrageparameter angeben. Das URL-Format finden Sie weiter unten:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Das Format für POST ist das Gleiche, allerdings mit der API-Version in den Parametern für die Abfragezeichenfolge.


## <a name="pulling-data-into-an-index"></a>Übertragen von Daten per Pullvorgang in einen Index
Beim Pull-Modell wird eine unterstützte Datenquelle durchsucht, und die Daten werden für Sie automatisch in Ihren Index hochgeladen. In Azure Search wird diese Funktion mit *Indexern* implementiert, die derzeit für diese Plattformen verfügbar sind:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Tabellenspeicherung](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL-Datenbank und SQL Server auf Azure-VMs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Mit Indexern wird für einen Index eine Verbindung mit einer Datenquelle (normalerweise eine Tabelle, Sicht oder ähnliche Struktur) hergestellt, und Quellfelder werden gleichwertigen Feldern im Index zugeordnet. Während der Ausführung wird das Rowset automatisch in JSON transformiert und in den angegebenen Index geladen. Alle Indexer unterstützen die Verwendung von Zeitplänen, sodass Sie angeben können, wie häufig die Daten aktualisiert werden sollen. Die meisten Indexer verfügen über eine Änderungsnachverfolgung, wenn dies von der Datenquelle unterstützt wird. Da Änderungen und Löschvorgänge in vorhandenen Dokumenten nachverfolgt und neue Dokumente erkannt werden, ist es mithilfe von Indexern nicht mehr erforderlich, die Daten in Ihrem Index aktiv zu verwalten. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Übertragen von Daten per Pullvorgang in einen Azure Search-Index

Die Indexer-Funktion steht im [Azure-Portal](search-import-data-portal.md) und für die [REST-API](/rest/api/searchservice/Indexer-operations) und das [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions) zur Verfügung. 

Ein Vorteil der Verwendung des Portals ist, dass Azure Search normalerweise ein Indexstandardschema für Sie generieren kann, indem die Metadaten des Quelldatasets gelesen werden. Sie können den generierten Index ändern, bis er verarbeitet wird. Nach der Verarbeitung sind nur noch Schemabearbeitungen zulässig, bei denen keine erneute Indizierung erforderlich ist. Wenn die gewünschten Änderungen eine direkte Auswirkung auf das Schema haben, müssen Sie den Index neu erstellen. 

## <a name="verify-data-import-with-search-explorer"></a>Überprüfen des Datenimports mit dem Suchexplorer

Eine schnelle Möglichkeit zum Durchführen einer vorläufigen Prüfung für den Dokumentupload ist die Verwendung des **Suchexplorers** im Portal. Mit dem Explorer können Sie einen Index abfragen, ohne dass Sie dafür Code schreiben müssen. Die Suche basiert auf den Standardeinstellungen, z.B. der [einfachen Syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) und dem [searchMode-Abfrageparameter](/rest/api/searchservice/search-documents), der standardmäßig verwendet wird. Ergebnisse werden im JSON-Format zurückgegeben, damit Sie das gesamte Dokument untersuchen können.

> [!TIP]
> Viele [Azure Search-Codebeispiele](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) enthalten eingebettete bzw. sofort verfügbare Datasets, damit Sie schnell starten können. Außerdem enthält das Portal auch einen Beispielindexer und eine Datenquelle mit einem kleinen Immobiliendataset („realestate-us-sample“). Wenn Sie den vorkonfigurierten Indexer in der Beispieldatenquelle ausführen, wird ein Index erstellt und mit Dokumenten geladen, die dann im Suchexplorer oder mit von Ihnen geschriebenem Code abgefragt werden können.

## <a name="see-also"></a>Weitere Informationen

+ [Indexer (Übersicht)](search-indexer-overview.md)
+ [Tutorial: Erstellen Ihres ersten Azure Search-Index im Portal](search-get-started-portal.md)
