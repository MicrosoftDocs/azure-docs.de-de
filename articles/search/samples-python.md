---
title: Python-Beispiele
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Python-Democodebeispiele für Azure Cognitive Search, für die das Azure .NET SDK für Python oder REST verwendet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955121"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-Codebeispiele für Azure Cognitive Search

Hier finden Sie Informationen zu den Python-Codebeispielen, die die Funktionen und den Workflow einer Azure Cognitive Search-Lösung veranschaulichen. In diesen Beispielen wird die [**Azure Cognitive Search-Clientbibliothek**](/python/api/overview/azure/search-documents-readme) für das [**Azure SDK für Python**](/azure/developer/python/) verwendet, die Sie über die folgenden Links erkunden können.

| Ziel | Link |
|--------|------|
| Paketdownload | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API-Referenz | [azure-search-documents](/python/api/azure-search-documents)  |
| API-Testfälle | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Quellcode | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK-Beispiele

Die Codebeispiele vom Azure SDK-Entwicklungsteam veranschaulichen die API-Verwendung. Sie finden diese Beispiele in GitHub unter [**azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples).

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Authentifizieren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Veranschaulicht, wie Sie einen Client konfigurieren und gegenüber dem Dienst authentifizieren. | 
| [Index: CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren, Löschen)](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Veranschaulicht, wie [Suchindizes](search-what-is-an-index.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden. |
| [Indexer: CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren, Löschen)](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Veranschaulicht, wie [Indexer](search-indexer-overview.md) erstellt, aktualisiert, abgerufen, aufgelistet, zurückgesetzt und gelöscht werden. |
| [Durchsuchen von Indexerdatenquellen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Veranschaulicht das Erstellen, Aktualisieren, Abrufen, Auflisten und Löschen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Synonyme](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Veranschaulicht, wie [Synonymzuordnungen](search-synonyms.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden.  |
| [Laden von Dokumenten](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Veranschaulicht, wie Dokumente in einen Index in einem [Datenimport](search-what-is-data-import.md)-Vorgang hochgeladen oder zusammengeführt werden. |
| [Einfache Abfrage](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Veranschaulicht, wie Sie eine [einfache Abfrage](search-query-overview.md) einrichten. |
| [Filterabfrage](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Veranschaulicht, wie Sie einen [Filterausdruck](search-filters.md) einrichten. |
| [Facettenabfrage](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Veranschaulicht, wie Sie [Facetten](search-filters-facets.md) verwenden. |

## <a name="doc-samples"></a>Dokumentationsbeispiele

Die Codebeispiele vom Cognitive Search-Team veranschaulichen die Funktionen und Workflows. Auf viele dieser Beispiele wird in Tutorials, Schnellstarts und Anleitungen verwiesen. Sie finden diese Beispiele in GitHub unter [**Azure-Samples/azure-search-postman-samples**](https://github.com/Azure-Samples/azure-search-python-samples).

| Beispiele | Artikel |
|---------|---------|
| [Schnellstart](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Quellcode für [Schnellstart: Erstellen eines Suchindex in Python](search-get-started-python.md). In diesem Artikel wird der allgemeine Workflow zum Erstellen, Laden und Abfragen eines Suchindex unter Verwendung von Beispieldaten beschrieben. |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Quellcode für [Tutorial: Verwenden von Python und KI zum Generieren von durchsuchbarem Inhalt über Azure-Blobs](cognitive-search-tutorial-blob-python.md). In diesem Artikel wird gezeigt, wie Sie einen Blobindexer mit einem Cognitive-Skillset erstellen, in dem das Skillset Rohdaten erstellt und transformiert, um sie durchsuchbar oder nutzbar zu machen. |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Quellcode für [Beispiel: Erstellen einer benutzerdefinierten Qualifikation mit Python](cognitive-search-custom-skill-python.md). Dieser Artikel veranschaulicht die Indexer- und Skillsetintegration mit Deep Learning-Modellen in Azure Machine Learning. |

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=python&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).