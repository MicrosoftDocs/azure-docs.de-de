---
title: Indexer für das Crawlen von Daten während des Imports
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie Azure SQL-Datenbank, SQL Managed Instance, Azure Cosmos DB oder Azure-Speicher per Crawler durchlaufen, um durchsuchbare Daten zu extrahieren und einen Azure Cognitive Search-Index aufzufüllen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 305682812896bb74474b5065cfd56a071a73ed15
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358778"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexer in Azure Cognitive Search

Ein *Indexer* in Azure Cognitive Search ist ein Crawler, mit dem durchsuchbare Daten und Metadaten aus einer externen Azure-Datenquelle extrahiert werden und ein Index basierend auf Feld-zu-Feld-Zuordnungen zwischen dem Index und Ihrer Datenquelle aufgefüllt wird. Dieser Ansatz wird auch als „Pullmodell“ bezeichnet, weil der Dienst Daten abruft, ohne dass Sie Code schreiben müssen, der einem Index Daten hinzufügt.

Indexer basieren auf Datenquellentypen oder Plattformen. Es gibt individuelle Indexer für SQL Server in Azure, Cosmos DB, Azure Table Storage und Azure Blob Storage. Blob Storage-Indexer verfügen über zusätzliche Eigenschaften, die für Blobinhaltstypen spezifisch sind.

Sie können einen Indexer als alleiniges Mittel für die Datenerfassung verwenden, oder Sie können eine Kombination aus Verfahren nutzen, bei denen ein Indexer zum Laden eines Teils der Felder in Ihren Index verwendet wird.

Sie können Indexer bei Bedarf oder nach einem Zeitplan für die regelmäßige Datenaktualisierung ausführen (z. B. alle fünf Minuten). Für häufigere Aktualisierungen ist ein Pushmodell erforderlich, bei dem Daten in Azure Cognitive Search und Ihrer externen Datenquelle gleichzeitig aktualisiert werden.

## <a name="approaches-for-creating-and-managing-indexers"></a>Ansätze zum Erstellen und Verwalten von Indexern

Indexer können mit folgenden Tools erstellt und verwaltet werden:

* [Portal > Datenimport-Assistent ](search-import-data-portal.md)
* [Dienst-REST-API](/rest/api/searchservice/Indexer-operations)
* [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

Ein neuer Indexer wird zunächst als Vorschaufeature angekündigt. Vorschaufeatures werden in APIs (REST und .NET) eingeführt und in das Portal integriert, wenn sie die allgemeine Verfügbarkeit erreicht haben. Planen Sie bei der Bewertung eines neuen Indexers das Schreiben von Code ein.

## <a name="permissions"></a>Berechtigungen

Alle Vorgänge im Zusammenhang mit Indexern, einschließlich GET-Anforderungen für den Status oder Definitionen, erfordern einen [Admin-API-Schlüssel](search-security-api-keys.md).

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Unterstützte Datenquellen

Indexer durchforsten Datenspeicher in Azure.

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (in der Vorschau)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL-Datenbank](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
* [SQL Server auf virtuellen Azure-Computern](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="indexer-stages"></a>Indexerphasen

Bei einer anfänglichen Ausführung, bei der der Index leer ist, liest ein Indexer alle Daten, die in der Tabelle oder im Container bereitgestellt werden. Bei nachfolgenden Ausführungen kann der Indexer in der Regel nur die geänderten Daten erkennen und abrufen. Bei Blobdaten erfolgt die Änderungserkennung automatisch. Bei anderen Datenquellen wie Azure SQL oder Cosmos DB muss die Änderungserkennung aktiviert werden.

Für jedes Dokument, das ein Indexer erfasst, implementiert oder koordiniert er mehrere Schritte, vom Abrufen des Dokuments bis hin zu einer endgültigen Suchmaschinenübergabe für die Indizierung. Optional ist ein Indexer auch bei der Initiierung von Skillset-Ausführungen und -Ausgaben dienlich, vorausgesetzt, dass ein Skillset definiert ist.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexerphasen" border="false":::

### <a name="stage-1-document-cracking"></a>Phase 1: Dokumententschlüsselung

Dokumententschlüsselung ist der Vorgang des Öffnens von Dateien und des Extrahierens von Inhalt. Abhängig vom Typ der Datenquelle versucht der Indexer, verschiedene Vorgänge auszuführen, um potenziell indizierbaren Inhalt zu extrahieren.  

Beispiele:  

* Wenn es sich bei dem Dokument um einen Datensatz in einer [Azure SQL-Datenquelle](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) handelt, extrahiert der Indexer alle Felder für den Datensatz.
* Wenn das Dokument eine PDF-Datei in einer [Azure Blob Storage-Datenquelle](search-howto-indexing-azure-blob-storage.md) ist, extrahiert der Indexer den Text, die Bilder und die Metadaten für die Datei.
* Wenn das Dokument ein Datensatz in einer [Cosmos DB-Datenquelle](search-howto-index-cosmosdb.md) ist, extrahiert der Indexer die Felder und Unterfelder aus dem Cosmos DB-Dokument.

### <a name="stage-2-field-mappings"></a>Phase 2: Feldzuordnungen 

Ein Indexer extrahiert Text aus einem Quellfeld und sendet ihn an ein Zielfeld in einem Index oder Wissensspeicher. Wenn Feldnamen und -typen übereinstimmen, ist der Pfad klar. Möglicherweise möchten Sie aber unterschiedliche Namen oder Typen in der Ausgabe, in welchem Fall Sie dem Indexer mitteilen müssen, wie das Feld zugeordnet werden soll. Dieser Schritt erfolgt nach der Dokumententschlüsselung, aber vor Transformationen, wenn der Indexer aus den Quelldokumenten liest. Wenn Sie eine [Feldzuordnung](search-indexer-field-mappings.md) definieren, wird der Wert des Quellfelds unverändert an das Zielfeld gesendet. Feldzuordnungen sind optional.

### <a name="stage-3-skillset-execution"></a>Phase 3: Skillset-Ausführung

Skillset-Ausführung ist ein optionaler Schritt, der die integrierte oder benutzerdefinierte KI-Verarbeitung aufruft. Sie benötigen diese möglicherweise für die optische Zeichenerkennung (OCR) in Form einer Bildanalyse, oder Sie benötigen eventuelle eine Sprachübersetzung. Wie auch immer die Transformation aussieht, die Skillset-Ausführung ist der Zeitpunkt, an dem eine Anreicherung erfolgt. Wenn ein Indexer eine Pipeline ist, können Sie sich einen [Skillset](cognitive-search-defining-skillset.md) als „Pipeline innerhalb der Pipeline“ vorstellen. Ein Skillset verfügt über eine eigene Abfolge von Schritten, die als Skills bezeichnet werden.

### <a name="stage-4-output-field-mappings"></a>Phase 4: Ausgabefeldzuordnungen

Die Ausgabe eines Skillsets ist tatsächlich eine Struktur von Informationen, die als angereichertes Dokument bezeichnet wird. Mithilfe von Ausgabefeldzuordnungen können Sie auswählen, welche Teile dieser Struktur Feldern in Ihrem Index zugeordnet werden sollen. Erfahren Sie, wie Sie [Ausgabefeldzuordnungen definieren](cognitive-search-output-field-mapping.md).

Wie bei Feldzuordnungen, die Literalwerte aus den Quellfeldern den Zielfeldern zuordnen, weisen Ausgabefeldzuordnungen den Indexer an, wie die transformierten Werte im angereicherten Dokument den Zielfeldern im Index zugeordnet werden sollen. Anders als bei Feldzuordnungen, die als optional angesehen werden, müssen Sie immer eine Ausgabefeldzuordnung für jeglichen transformierten Inhalt definieren, der sich in einem Index befinden muss.

Die nächste Abbildung zeigt eine Beispieldarstellung einer Indexer-[Debugsitzung](cognitive-search-debug-session.md)der Indexerphasen: Dokumententschlüsselung, Feldzuordnungen, Skillset-Ausführung und Ausgabefeldzuordnungen.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="Beispieldebugsitzung" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Grundlegende Konfigurationsschritte

Indexer können Features bereitstellen, die für die Datenquelle eindeutig sind. In dieser Hinsicht variieren einige Aspekte von Indexern oder der Datenquellenkonfiguration nach Indexertyp. Für alle Indexer werden aber die gleiche grundlegende Zusammenstellung und die gleichen Anforderungen verwendet. Die Schritte, die für alle Indexer gelten, sind unten beschrieben.

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle
Ein Indexer ruft eine Datenquellenverbindung aus einem *Datenquelle*-Objekt ab. Die Datenquellendefinition enthält eine Verbindungszeichenfolge und ggf. Anmeldeinformationen. Rufen Sie die REST-API [Datenquelle erstellen](/rest/api/searchservice/create-data-source) oder die [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)-Klasse auf, um die Ressource zu erstellen.

Datenquellen werden unabhängig von den Indexern, die darauf zugreifen, konfiguriert und verwaltet. Dies bedeutet, dass eine Datenquelle von mehreren Indexern verwendet werden kann, um mehr als einen Index gleichzeitig zu laden.

### <a name="step-2-create-an-index"></a>Schritt 2: Erstellen eines Index
Mit einem Indexer werden einige Aufgaben in Bezug auf die Datenerfassung automatisiert, aber das Erstellen eines Index gehört im Allgemeinen nicht dazu. Als Voraussetzung hierfür müssen Sie über einen vordefinierten Index mit Feldern verfügen, die den Feldern in Ihrer externen Datenquelle entsprechen. Felder müssen nach Name und Datentyp übereinstimmen. Weitere Informationen zum Strukturieren eines Index finden Sie unter [Create Index (Azure Cognitive Search-REST-API)](/rest/api/searchservice/Create-Index) oder der [SearchIndex-Klasse](/dotnet/api/azure.search.documents.indexes.models.searchindex). Hilfreiche Informationen zu Feldzuordnungen finden Sie unter [Feldzuordnungen in Azure Cognitive Search-Indexern](search-indexer-field-mappings.md).

> [!Tip]
> Indexer können zwar keinen Index für Sie erstellen, diese Aufgabe kann jedoch mit dem Assistenten **Daten importieren** im Portal ausgeführt werden. In den meisten Fällen kann der Assistent ein Indexschema aus vorhandenen Metadaten in der Quelle ableiten und ein vorläufiges Indexschema bereitstellen, das Sie inline bearbeiten können, während der Assistent aktiv ist. Sobald der Index für den Dienst erstellt wurde, ist die weitere Bearbeitung im Portal hauptsächlich auf das Hinzufügen neuer Felder beschränkt. Verwenden Sie den Assistenten zum Erstellen, aber nicht zum Überarbeiten eines Index. Praxisnahe Lerninhalte finden Sie in der [exemplarischen Vorgehensweise zum Portal](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Schritt 3: Erstellen und Planen des Indexers
Die Indexerdefinition ist ein Konstrukt, das alle Elemente im Zusammenhang mit der Datenerfassung kombiniert. Erforderliche Elemente enthalten eine Datenquelle und einen Index. Optionale Elemente enthalten einen Zeitplan und Feldzuordnungen. Feldzuordnungen sind nur optional, wenn sich Quellfelder und Indexfelder eindeutig entsprechen. Weitere Informationen zum Strukturieren eines Indexers finden Sie unter [Create Indexer (Azure Cognitive Search-REST-API)](/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Ausführen von Indexern bei Bedarf

Während es üblich ist, eine Indizierung zu planen, kann ein Indexer auch bei Bedarf mit dem [Befehl „Ausführen“](/rest/api/searchservice/run-indexer) aufgerufen werden:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Wenn die Ausführungs-API erfolgreich ausgeführt wurde, wurde der Indexeraufruf geplant, die eigentliche Verarbeitung erfolgt jedoch asynchron. 

Sie können den Indexerstatus im Portal oder über die API zum Abrufen des Indexer-Status überwachen. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Abrufen des Indexerstatus

Sie können den Status und Ausführungsverlauf eines Indexers über den [Befehl „Indexer-Status abrufen“](/rest/api/searchservice/get-indexer-status) abrufen:

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Die Antwort enthält den Gesamtstatus des Indexers, den letzten (oder laufenden) Aufruf des Indexers sowie den Verlauf der letzten Indexeraufrufe.

```output
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
```

Der Ausführungsverlauf enthält bis zu 50 der jüngsten abgeschlossenen Ausführungen. Diese sind in umgekehrter chronologischer Reihenfolge sortiert (somit ist die neueste Ausführung als Erstes in der Antwort aufgelistet).

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich über die Grundlagen informiert haben, ist der nächste Schritt das Überprüfen der Anforderungen und Aufgaben für jeden Datenquellentyp.

* [Azure SQL-Datenbank, SQL Managed Instance oder SQL Server auf einem virtuellen Azure-Computer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Indizieren von CSV-Blobs mit Azure Cognitive Search-Blobindexer](search-howto-index-csv-blobs.md)
* [Indizieren von JSON-Blobs mit Azure Cognitive Search-Blobindexer](search-howto-index-json-blobs.md)