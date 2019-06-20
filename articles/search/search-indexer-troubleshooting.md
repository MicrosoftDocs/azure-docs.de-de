---
title: Behandeln von häufigen Problemen mit Suchindexern – Azure Search
description: Beheben Sie Fehler und häufige Problemen mit Indexern in Azure Search, einschließlich Datenquellenverbindung, Firewall und fehlende Dokumente.
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 256a38320c9b3ca826ee9c12ac0a437957f988e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539291"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Behandeln von häufigen Problemen mit Indexern in Azure Search

Indexer können bei der Indizierung von Daten in Azure Search auf diverse Probleme stoßen. Die Hauptfehlerkategorien umfassen:

* [Herstellen einer Verbindung mit Datenquellen](#data-source-connection-errors)
* [Verarbeiten von Dokumenten](#document-processing-errors)
* [Erfassen von Dokumenten in einem Index](#index-errors)

## <a name="data-source-connection-errors"></a>Fehler bei Datenquellenverbindungen

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Speicherkontofirewall

Azure Storage stellt eine konfigurierbare Firewall bereit. Die Firewall ist standardmäßig deaktiviert, sodass Azure Search eine Verbindung mit Ihrem Speicherkonto herstellen kann.

Sie erhalten keine spezifische Fehlermeldung, wenn die Firewall aktiviert ist. Firewallfehler sehen in der Regel so aus: `The remote server returned an error: (403) Forbidden`.

Sie können im [Portal](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal) überprüfen, ob die Firewall aktiviert ist. Wenn die Firewall aktiviert ist, haben Sie zwei Möglichkeiten, das Problem zu umgehen:

1. Deaktivieren Sie die Firewall, indem Sie festlegen, dass [Alle Netzwerke](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal) zugreifen dürfen.
1. [Fügen Sie eine Ausnahme](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) für die IP-Adresse Ihres Suchdiensts hinzu. Um diese IP-Adresse zu finden, verwenden Sie den folgenden Befehl:

`nslookup <service name>.search.windows.net`

Ausnahmen können nicht in der [kognitiven Suche](cognitive-search-concept-intro.md) verwendet werden. Da kann das Problem nur umgangen werden, indem die Firewall deaktiviert wird.

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Nicht aktivierte Indizierung

Azure Search steht in impliziter Abhängigkeit zur Indizierung von Cosmos DB. Wenn Sie die automatische Indizierung in Cosmos DB deaktivieren, gibt Azure Search einen erfolgreichen Status zurück, kann jedoch keine Containerinhalte indizieren. Anweisungen zum Überprüfen der Einstellungen und zum Aktivieren der Indizierung finden Sie unter [Verwalten der Indizierung in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#manage-indexing-using-azure-portal).

## <a name="document-processing-errors"></a>Fehler bei der Dokumentverarbeitung

### <a name="unprocessable-or-unsupported-documents"></a>Nicht verarbeitbare oder nicht unterstützte Dokumente

Der Blobindexer [ dokumentiert, welche Dokumentformate explizit unterstützt werden](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Gelegentlich enthält ein Blobspeichercontainer nicht unterstützte Dokumente. Manchmal sind die Dokumente fehlerhaft. Sie können verhindern, dass Ihr Indexer für diese Dokumente beendet wird, indem Sie die [Konfigurationsoptionen](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) ändern:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Fehlende Dokumentinhalte

Der Blobindexer [ findet und extrahiert Text aus Blobs in einem Container](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Beim Extrahieren von Text können u.a. diese Probleme auftreten:

* Das Dokument enthält nur gescannte Bilder. PDF-Blobs mit Nichttextinhalten wie gescannten Bildern (JPGs) erzeugen keine Ergebnisse in einer Standard-Blobindizierungspipeline. Wenn Sie Bildinhalte mit Textelementen haben, können Sie mithilfe der [kognitiven Suche](cognitive-search-concept-image-scenarios.md) Text finden und extrahieren.
* Der Blobindexer ist so konfiguriert, dass er nur Metadaten indiziert. Zum Extrahieren von Inhalten muss der Blobindexer so konfiguriert sein, dass er [sowohl Inhalte als auch Metadaten extrahiert](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexfehler

### <a name="missing-documents"></a>Fehlende Dokumente

Indexer finden Dokumente in einer [Datenquelle](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Manchmal fehlt ein Datenquellendokument, das indiziert werden sollte, in einem Index. Es gibt mehrere Gründe für das Auftreten dieser Fehler:

* Das Dokument wurde nicht indiziert. Überprüfen Sie das Portal auf eine erfolgreiche Indexerausführung hin.
* Das Dokument wurde nach der Ausführung des Indexers aktualisiert. Wenn Sie für Ihren Indexer einen [Zeitplan](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule) festgelegt haben, wird er das Dokument schließlich erneut ausführen und abrufen.
* Die in der Datenquelle angegebene [Abfrage](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) schließt das Dokument aus. Indexer können keine Dokumente indizieren, die nicht zur Datenquelle gehören.
* Das Dokument wurde durch [Feldzuordnungen](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) oder die [kognitive Suche](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) geändert und sieht anders aus, als Sie erwartet haben.
* Verwenden Sie zur Dokumentensuche die [API zum Suchen von Dokumenten](https://docs.microsoft.com/rest/api/searchservice/lookup-document).
