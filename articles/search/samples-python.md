---
title: Python-Beispiele
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Python-Democodebeispiele für Azure Cognitive Search, für die das Azure .NET SDK für Python oder REST verwendet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6e0f3d318cc462b03151d5a4935ae318df46e2c5
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510555"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-Codebeispiele für Azure Cognitive Search

Dieser Artikel enthält Python-Codebeispiele, in denen die Features und Funktionen von Azure Cognitive Search veranschaulicht werden. Primäre Repositorys:

| Repository | BESCHREIBUNG |
|------------|-------------|
| [azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Vom Azure SDK-Team erstellte Beispiele, die in der Clientbibliothek „Azure.Search.Documents“ des SDK enthalten sind. Anhand von [Komponententests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) für die Clientbibliothek können Sie sich außerdem ansehen, wie verschiedene APIs aufgerufen werden. |
| [Azure-Samples/azure-search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples) | Codebeispiele zu Artikeln mit Anleitungen, z. B. [Schnellstart: Erstellen eines Suchindex in Python](search-get-started-python.md).|

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=python&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="python-sdk-samples"></a>Python SDK-Beispiele

Das Azure SDK für Python enthält viele verschiedene Beispiele und eine [Seite mit den ersten Schritten](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples), auf der Sie Informationen zu den Voraussetzungen und zur Paketinstallation finden. Die Seite enthält auch Links zu den unten aufgeführten Beispielen, die hier als Hilfe für Sie angegeben sind.

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

## <a name="documentation-samples"></a>Dokumentationsbeispiele

Zu den folgenden Beispielen ist ein entsprechender Artikel in der [Dokumentation zu Azure Cognitive Search](./index.yml) vorhanden.

| Beispiele | BESCHREIBUNG | 
|---------|-------------|
| [Schnellstart](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Quellcode für [Schnellstart: Erstellen eines Suchindex in Python](search-get-started-python.md).  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Quellcode für [Tutorial: Verwenden von Python und KI zum Generieren von durchsuchbarem Inhalt über Azure-Blobs](cognitive-search-tutorial-blob-python.md).  |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Quellcode für [Beispiel: Erstellen einer benutzerdefinierten Qualifikation mit Python](cognitive-search-custom-skill-python.md).  |