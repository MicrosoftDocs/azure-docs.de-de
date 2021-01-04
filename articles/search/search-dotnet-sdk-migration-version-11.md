---
title: Upgrade auf .NET SDK-Version 11
titleSuffix: Azure Cognitive Search
description: Migrieren Sie Code von älteren Versionen zum Azure Cognitive Search .NET SDK-Version 11. Hier finden Sie Informationen zu Neuheiten und erfahren, welche Änderungen am Code erforderlich sind.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 260df85f3e380e40d153fc17ce77bd56ca068982
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532821"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Upgrade auf Version 11 des Azure Cognitive Search .NET SDK

Wenn Sie Version 10.0 oder eine ältere Version des [.NET SDK](/dotnet/api/overview/azure/search) verwenden, hilft Ihnen dieser Artikel dabei, ein Upgrade auf Version 11 und die Clientbibliothek **Azure.Search.Documents** durchzuführen.

Version 11 ist eine vollständig neu gestaltete Clientbibliothek, die vom Azure SDK-Entwicklungsteam freigegeben wurde (frühere Versionen wurden vom Azure Cognitive Search-Entwicklungsteam erstellt). Die Bibliothek wurde neu gestaltet, um eine größere Konsistenz mit anderen Azure-Clientbibliotheken zu erreichen. Dabei wurde eine Abhängigkeit von [Azure.Core](/dotnet/api/azure.core) und [System.Text.Json](/dotnet/api/system.text.json) berücksichtigt und bewährte Ansätze für gängige Aufgaben implementiert.

Zu den wichtigsten Unterschieden, die Ihnen in der neuen Version auffallen werden, gehören:

+ Ein Paket und eine Bibliothek im Gegensatz zu mehreren
+ Ein neuer Paketname: `Azure.Search.Documents` anstelle von `Microsoft.Azure.Search`
+ Drei Clients anstelle von zwei: `SearchClient`, `SearchIndexClient`, `SearchIndexerClient`
+ Benennungsunterschiede bei verschiedenen APIs und kleine strukturelle Unterschiede, die einige Aufgaben vereinfachen

> [!NOTE]
> Überprüfen Sie das [**Änderungsprotokoll**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md), um eine Liste der Änderungen in .NET SDK Version 11 anzuzeigen.

## <a name="package-and-library-consolidation"></a>Paket- und Bibliothekskonsolidierung

Version 11 fasst mehrere Pakete und Bibliotheken in jeweils ein Paket bzw. eine Bibliothek zusammen. Nach der Migration müssen Sie weniger Bibliotheken verwalten.

+ [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [API-Referenz für die Clientbibliothek](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>Clientunterschiede

In der folgenden Tabelle werden die Clientbibliotheken soweit möglich einer der beiden Versionen zugeordnet.

| Umfang von Vorgängen | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| Client für Abfragen und zum Auffüllen eines Index | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Client für Indizes, Analysetools, Synonymzuordnungen | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Client für Indexer, Datenquellen, Skillsets | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**neu**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` ist in beiden Versionen vorhanden, unterstützt aber unterschiedliche Dinge. In Version 10 erstellt `SearchIndexClient` Indizes und andere Objekte. In Version 11 arbeitet `SearchIndexClient` mit vorhandenen Indizes. Achten Sie auf die Reihenfolge, in der die Clientverweise aktualisiert werden, damit keine Unklarheiten entstehen. Befolgen Sie die Reihenfolge in den [Schritten zum Aktualisieren](#UpgradeSteps), um Probleme beim Ersetzen von Zeichenfolgen zu vermeiden.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Benennung und andere API-Unterschiede

Abgesehen von den Clientunterschieden (die bereits erwähnt und daher hier weggelassen werden), wurden mehrere andere APIs umbenannt und in einigen Fällen überarbeitet. Die Unterschiede bei den Klassennamen sind unten zusammengefasst. Diese Liste ist nicht vollständig, aber darin werden API-Änderungen nach Aufgaben gruppiert, was für Revisionen bei bestimmten Codeblöcken hilfreich sein kann. Eine detaillierte Liste der API-Updates finden Sie im [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) für `Azure.Search.Documents` auf GitHub.

### <a name="authentication-and-encryption"></a>Authentifizierung und Verschlüsselung

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (war im [Vorschau-SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) als allgemein verfügbares Feature vorhanden) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indizes, Analysetools, Synonymzuordnungen

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Feld](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (auch `AnalyzerName` in `LexicalAnalyzerName`) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (auch `StandardTokenizerV2` in `LuceneStandardTokenizerV2`) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (auch `TokenizerName` in `LexicalTokenizerName`) |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Keine. Verweise auf `Format` entfernen. |

Felddefinitionen sind optimiert: [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) sind neue APIs zur Erstellung von Felddefinitionen.

### <a name="indexers-datasources-skillsets"></a>Indexer, Datenquellen, Skillsets

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [Indexer](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Skill](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Qualifikationsgruppe](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Datenimport

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Abfragedefinitionen und -ergebnisse

| Version 10 | Entsprechung in Version 11 |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) oder [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), abhängig davon, ob das Ergebnis ein einziges Dokument oder mehrere Dokumente ist. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Neuerungen in Version 11

Jede Version einer Azure Cognitive Search-Clientbibliothek ist auf eine entsprechende Version der REST-API ausgelegt. Die REST-API gilt als Basis für den Dienst, wobei einzelne SDKs eine Version der REST-API umschließen. Für .NET-Entwickler kann es hilfreich sein, die [REST-API-Dokumentation](/rest/api/searchservice/) durchzusehen, wenn Sie mehr Hintergrundinformationen zu bestimmten Objekten oder Vorgängen benötigen.

Version 11 ist auf den [2020-06-30-Suchdienst](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json) ausgelegt. Da es sich bei Version 11 auch um eine von Grund auf neu erstellte Clientbibliothek handelt, konzentriert sich der größte Teil der Entwicklungsarbeit auf die Äquivalenz mit Version 10, wobei die Unterstützung einiger REST-API-Features noch aussteht.

Version 11.0 bietet vollständige Unterstützung für die folgenden Objekte und Vorgänge:

+ Indexerstellung und -verwaltung
+ Erstellung und Verwaltung von Synonymzuordnungen
+ Jegliche Abfragetypen und Syntax (ausgenommen räumliche Filter)
+ Indexerobjekte und -vorgänge zur Indizierung von Azure-Datenquellen, einschließlich Datenquellen und Skillsets

In Version 11.1 kommt Folgendes hinzu:

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (in 11.1 hinzugefügt)
+ [Serialisierungseigenschaft](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (in 11.1 hinzugefügt) zur Unterstützung der benutzerdefinierten Serialisierung

### <a name="pending-features"></a>Ausstehende Features

Die folgenden Features der Version 10 sind noch nicht in Version 11 verfügbar. Wenn Sie diese Features benötigen, warten Sie mit der Migration, bis diese unterstützt werden.

+ Räumliche Typen
+ [Wissensspeicher](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Schritte zum Upgrade

Mit den folgenden Schritten können Sie eine Codemigration beginnen, indem Sie die ersten erforderlichen Schritte absolvieren (insbesondere im Hinblick auf Clientverweise).

1. Installieren Sie das [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/), indem Sie mit der rechten Maustaste auf Ihre Projektreferenzen klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

1. Ersetzen Sie die using-Anweisungen für Microsoft.Azure.Search durch die folgenden:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Ersetzen Sie für Klassen, die eine JSON-Serialisierung erfordern, `using Newtonsoft.Json` durch `using System.Text.Json.Serialization`.

1. Überarbeiten Sie den Authentifizierungscode für den Client. In früheren Versionen verwendeten Sie Eigenschaften im Clientobjekt, um den API-Schlüssel festzulegen (z. B. die [SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials)-Eigenschaft). In der aktuellen Version verwenden Sie die [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)-Klasse, um den Schlüssel als Anmeldeinformationen zu übergeben, sodass Sie bei Bedarf den API-Schlüssel aktualisieren können, ohne neue Clientobjekte zu erstellen.

   Clienteigenschaften wurden (soweit möglich) auf `Endpoint`, `ServiceName` und `IndexName` optimiert. Im folgenden Beispiel wird die [Uri](/dotnet/api/system.uri)-Systemklasse verwendet, um den Endpunkt anzugeben, und die [Environment](/dotnet/api/system.environment)-Klasse, um den Schlüsselwert einzulesen:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Fügen Sie neue Clientverweise für indexerbezogene Objekte hinzu. Wenn Sie Indexer, Datenquellen oder Skillsets verwenden, ändern Sie die Clientverweise in [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Dieser Client ist in Version 11 neu und hat keine Vorgängerversion.

1. Überarbeiten Sie Sammlungen und Listen. Im neuen SDK sind alle Listen schreibgeschützt, um Downstreamprobleme zu vermeiden, wenn die Liste NULL-Werte enthält. Der Code muss so geändert werden, dass Elemente in eine Liste eingefügt werden. Anstatt beispielsweise einer Select-Eigenschaft Zeichenfolgen zuzuweisen, fügen Sie diese wie folgt hinzu:

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   „Select“, „Facets“, „SearchFields“, „SourceFields“, „ScoringParameters“ und „OrderBy“ sind jeweils Listen, die nun rekonstruiert werden müssen.

1. Aktualisieren Sie Clientverweise für Abfragen und Datenimport. Instanzen von [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) müssen in [SearchClient](/dotnet/api/azure.search.documents.searchclient) geändert werden. Stellen Sie sicher, dass Sie alle Instanzen abfangen, bevor Sie mit dem nächsten Schritt fortfahren, um Namensverwechslungen zu vermeiden.

1. Aktualisieren Sie Clientverweise für Index-, Synonymzuordnungs- und Analysetoolobjekte. Instanzen von [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) müssen in [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) geändert werden. 

1. Aktualisieren Sie in Ihrem restlichen Code die Klassen, Methoden und Eigenschaften, um die APIs der neuen Bibliothek zu verwenden. Sie können mit dem Abschnitt [Benennungsunterschiede](#naming-differences) beginnen oder auch das [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) einsehen.

   Wenn Sie Schwierigkeiten haben, entsprechende APIs zu finden, schlagen wir vor, ein Problem unter [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) zu melden, damit wir die Dokumentation verbessern oder das Problem untersuchen können.

1. Generieren Sie die Projektmappe neu. Wenn Sie alle Buildfehler bzw. Warnungen behoben haben, können Sie weitere Änderungen an Ihrer Anwendung vornehmen, um die [neue Funktionalität](#WhatsNew) zu nutzen.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Breaking Changes in Version 11

Angesichts der tiefgreifenden Änderungen an Bibliotheken und APIs ist ein Upgrade auf Version 11 nicht trivial und stellt einen Breaking Change in der Hinsicht dar, dass Ihr Code nicht mehr abwärtskompatibel mit Version 10 und früheren Versionen ist. Eine ausführliche Übersicht über die Unterschiede finden Sie in dem [Änderungsprotokoll](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) für `Azure.Search.Documents`.

Hinsichtlich der Dienstversionsupdates, die Codeänderungen in Version 11 für vorhandene Funktionen (und nicht nur ein Refactoring der APIs) nach sich ziehen, treten die folgenden Verhaltensänderungen auf:

+ Der [BM25-Ähnlichkeitsalgorithmus für die Rangfolge](index-ranking-similarity.md) ersetzt den bisherigen Rangfolgealgorithmus durch neuere Technologie. Neue Dienste verwenden diesen Algorithmus automatisch. Für vorhandene Dienste müssen Sie Parameter festlegen, um den neuen Algorithmus zu verwenden.

+ Die Behandlung von Nullwerten in [sortierten Ergebnissen](search-query-odata-orderby.md) hat sich in dieser Version geändert, Nullwerte werden bei `asc`-Sortierung zuerst und bei `desc`-Sortierung zuletzt angezeigt. Wenn Sie die Handhabung von Nullwerten in Code geschrieben haben, sollten Sie diesen überprüfen und möglicherweise entfernen, wenn er nicht mehr erforderlich ist.

## <a name="next-steps"></a>Nächste Schritte

+ [Azure.Search.Documents-Paket](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Beispiele bei GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document-API-Referenz](/dotnet/api/overview/azure/search.documents-readme)