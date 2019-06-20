---
title: 'Indexer für das Crawlen von Datenquellen während der Indizierung: Azure Search'
description: Erfahren Sie, wie Sie eine Azure SQL-Datenbank, Azure Cosmos DB oder Azure-Speicher per Crawler durchlaufen, um durchsuchbare Daten zu extrahieren und einen Azure Search-Index aufzufüllen.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 87e35573eea836fc8a88c7515409c070ec63aa3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024893"
---
# <a name="indexers-in-azure-search"></a>Indexer in Azure Search

Ein *Indexer* in Azure Search ist ein Crawler, mit dem durchsuchbare Daten und Metadaten aus einer externen Azure-Datenquelle extrahiert werden und ein Index basierend auf Feld-zu-Feld-Zuordnungen zwischen dem Index und Ihrer Datenquelle aufgefüllt wird. Dieser Ansatz wird auch als „Pullmodell“ bezeichnet, weil der Dienst Daten abruft, ohne dass Sie Code schreiben müssen, der einem Index Daten hinzufügt.

Indexer basieren auf Datenquellentypen oder Plattformen. Es gibt individuelle Indexer für SQL Server in Azure, Cosmos DB, Azure Table Storage und Azure Blob Storage. Blob Storage-Indexer verfügen über zusätzliche Eigenschaften, die für Blobinhaltstypen spezifisch sind.

Sie können einen Indexer als alleiniges Mittel für die Datenerfassung verwenden, oder Sie können eine Kombination aus Verfahren nutzen, bei denen ein Indexer zum Laden eines Teils der Felder in Ihren Index verwendet wird.

Sie können Indexer bei Bedarf oder nach einem Zeitplan für die regelmäßige Datenaktualisierung ausführen, z.B. alle 15 Minuten. Für häufigere Aktualisierungen ist ein Pushmodell erforderlich, bei dem Daten in Azure Search und Ihrer externen Datenquelle gleichzeitig aktualisiert werden.

## <a name="approaches-for-creating-and-managing-indexers"></a>Ansätze zum Erstellen und Verwalten von Indexern

Indexer können mit folgenden Tools erstellt und verwaltet werden:

* [Portal > Datenimport-Assistent ](search-import-data-portal.md)
* [Dienst-REST-API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Ein neuer Indexer wird zunächst als Vorschaufeature angekündigt. Vorschaufeatures werden in APIs (REST und .NET) eingeführt und in das Portal integriert, wenn sie die allgemeine Verfügbarkeit erreicht haben. Planen Sie bei der Bewertung eines neuen Indexers das Schreiben von Code ein.

## <a name="permissions"></a>Berechtigungen

Alle Vorgänge im Zusammenhang mit Indexern, einschließlich GET-Anforderungen für den Status oder Definitionen, erfordern einen [Admin-API-Schlüssel](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

Indexer durchforsten Datenspeicher in Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) 

> [!Note]
> Azure Table Storage wird für die [kognitive Suche](cognitive-search-concept-intro.md) nicht unterstützt.
>

## <a name="basic-configuration-steps"></a>Grundlegende Konfigurationsschritte
Indexer können Features bereitstellen, die für die Datenquelle eindeutig sind. In dieser Hinsicht variieren einige Aspekte von Indexern oder der Datenquellenkonfiguration nach Indexertyp. Für alle Indexer werden aber die gleiche grundlegende Zusammenstellung und die gleichen Anforderungen verwendet. Die Schritte, die für alle Indexer gelten, sind unten beschrieben.

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle
Ein Indexer ruft eine Datenquellenverbindung aus einem *Datenquelle*-Objekt ab. Die Datenquellendefinition enthält eine Verbindungszeichenfolge und ggf. Anmeldeinformationen. Rufen Sie die REST-API [Datenquelle erstellen](https://docs.microsoft.com/rest/api/searchservice/create-data-source) oder die [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource)-Klasse auf, um die Ressource zu erstellen.

Datenquellen werden unabhängig von den Indexern, die darauf zugreifen, konfiguriert und verwaltet. Dies bedeutet, dass eine Datenquelle von mehreren Indexern verwendet werden kann, um mehr als einen Index gleichzeitig zu laden.

### <a name="step-2-create-an-index"></a>Schritt 2: Erstellen eines Index
Mit einem Indexer werden einige Aufgaben in Bezug auf die Datenerfassung automatisiert, aber das Erstellen eines Index gehört im Allgemeinen nicht dazu. Als Voraussetzung hierfür müssen Sie über einen vordefinierten Index mit Feldern verfügen, die den Feldern in Ihrer externen Datenquelle entsprechen. Felder müssen nach Name und Datentyp übereinstimmen. Weitere Informationen zum Strukturieren eines Index finden Sie unter [Create Index (Azure Search-Dienst REST-API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) oder [Index-Klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Hilfreiche Informationen zu Feldzuordnungen finden Sie unter [Feldzuordnungen in Azure Search-Indexern](search-indexer-field-mappings.md).

> [!Tip]
> Indexer können zwar keinen Index für Sie erstellen, diese Aufgabe kann jedoch mit dem Assistenten **Daten importieren** im Portal ausgeführt werden. In den meisten Fällen kann der Assistent ein Indexschema aus vorhandenen Metadaten in der Quelle ableiten und ein vorläufiges Indexschema bereitstellen, das Sie inline bearbeiten können, während der Assistent aktiv ist. Sobald der Index für den Dienst erstellt wurde, ist die weitere Bearbeitung im Portal hauptsächlich auf das Hinzufügen neuer Felder beschränkt. Verwenden Sie den Assistenten zum Erstellen, aber nicht zum Überarbeiten eines Index. Praxisnahe Lerninhalte finden Sie in der [exemplarischen Vorgehensweise zum Portal](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Schritt 3: Erstellen und Planen des Indexers
Die Indexerdefinition ist ein Konstrukt, das alle Elemente im Zusammenhang mit der Datenerfassung kombiniert. Erforderliche Elemente enthalten eine Datenquelle und einen Index. Optionale Elemente enthalten einen Zeitplan und Feldzuordnungen. Feldzuordnungen sind nur optional, wenn sich Quellfelder und Indexfelder eindeutig entsprechen. Ein Indexer kann von einem anderen Dienst aus auf eine Datenquelle verweisen, solange diese Datenquelle aus demselben Abonnement stammt. Weitere Informationen zum Strukturieren eines Indexers finden Sie unter [Create Indexer (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)(Create Indexer (Azure Search REST-API)).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Ausführen von Indexern bei Bedarf

Während es üblich ist, eine Indizierung zu planen, kann ein Indexer auch bei Bedarf mit dem [Befehl „Ausführen“](https://docs.microsoft.com/rest/api/searchservice/run-indexer) aufgerufen werden:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Wenn die Ausführungs-API erfolgreich ausgeführt wurde, wurde der Indexeraufruf geplant, die eigentliche Verarbeitung erfolgt jedoch asynchron. 

Sie können den Indexerstatus im Portal oder über die API zum Abrufen des Indexer-Status überwachen. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Abrufen des Indexerstatus

Sie können den Status und Ausführungsverlauf eines Indexers über den [Befehl „Indexer-Status abrufen“](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) abrufen:


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Die Antwort enthält den Gesamtstatus des Indexers, den letzten (oder laufenden) Aufruf des Indexers sowie den Verlauf der letzten Indexeraufrufe.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Der Ausführungsverlauf enthält bis zu 50 der jüngsten abgeschlossenen Ausführungen. Diese sind in umgekehrter chronologischer Reihenfolge sortiert (somit ist die neueste Ausführung als Erstes in der Antwort aufgelistet).

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich über die Grundlagen informiert haben, ist der nächste Schritt das Überprüfen der Anforderungen und Aufgaben für jeden Datenquellentyp.

* [Azure SQL-Datenbank oder SQL Server auf einem virtuellen Azure-Computer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indizieren von CSV-Blobs mit Azure Search-Blobindexer](search-howto-index-csv-blobs.md)
* [Indizieren von JSON-Blobs mit Azure Search-Blobindexer](search-howto-index-json-blobs.md)
