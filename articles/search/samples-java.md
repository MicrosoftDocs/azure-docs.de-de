---
title: Beispiele für Java
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Java-Democodebeispiele für Azure Cognitive Search, für die das Azure .NET SDK für Java verwendet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955036"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Java-Codebeispiele für Azure Cognitive Search

Hier finden Sie Informationen zu den Java-Codebeispielen, die die Funktionen und den Workflow einer Azure Cognitive Search-Lösung veranschaulichen. In diesen Beispielen wird die [**Azure Cognitive Search-Clientbibliothek**](/java/api/overview/azure/search-documents-readme) für das [**Azure SDK für .Java**](/azure/developer/java/sdk) verwendet, die Sie über die folgenden Links erkunden können.

| Ziel | Link |
|--------|------|
| Paketdownload | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API-Referenz | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API-Testfälle | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Quellcode | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK-Beispiele

Die Codebeispiele vom Azure SDK-Entwicklungsteam veranschaulichen die API-Verwendung. Sie finden diese Beispiele in GitHub unter [**Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples).

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Suchindexerstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Veranschaulicht das Erstellen von [Suchindizes](search-what-is-an-index.md). |
| [Synonymerstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Veranschaulicht das Erstellen von [Synonymzuordnungen](search-synonyms.md).  |
| [Suchindexererstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Veranschaulicht das Erstellen von [Indexern](search-indexer-overview.md). |
| [Datenquellenerstellung für Suchindexer](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Veranschaulicht das Erstellen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Skillseterstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [Laden von Dokumenten](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Veranschaulicht das Hochladen oder Zusammenführen von Dokumenten in einen Index in einem [Datenimport](search-what-is-data-import.md)-Vorgang. |
| [Abfragesyntax](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Veranschaulicht das Einrichten einer [einfachen Abfrage](search-query-overview.md). |

## <a name="doc-samples"></a>Dokumentationsbeispiele

Die Codebeispiele vom Cognitive Search-Team veranschaulichen die Funktionen und Workflows. Auf viele dieser Beispiele wird in Tutorials, Schnellstarts und Anleitungen verwiesen. Sie finden diese Beispiele in GitHub unter [**Azure-Samples/azure-search-java-samples**](https://github.com/Azure-Samples/azure-search-java-samples).

| Beispiele | Artikel | 
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Quellcode für [Schnellstart: Erstellen eines Suchindex in Java und REST](search-get-started-java.md). Dieses Beispiel wurde nicht für das Java SDK aktualisiert. Es werden die REST-APIs aufgerufen. |

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=java&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="other-samples"></a>Weitere Beispiele

Die folgenden Beispiele werden ebenfalls vom Cognitive Search-Team veröffentlicht, ohne dass jedoch in der Dokumentation auf sie Bezug genommen wird. Die zugehörigen Infodateien enthalten Anweisungen zu ihrer Verwendung.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [search-java-getting-started](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Verwendet die Java SDK-Clientbibliothek, um einen Suchindex zu erstellen, zu laden und abzufragen. Dieses Beispiel ist derzeit eigenständig. |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Veranschaulicht einen Azure Cosmos DB-Indexer in Java. Dieses Beispiel wurde nicht für das Java SDK aktualisiert. Es werden die REST-APIs aufgerufen.|