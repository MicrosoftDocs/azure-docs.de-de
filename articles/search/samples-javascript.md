---
title: JavaScript-Beispiele
titleSuffix: Azure Cognitive Search
description: Hier finden Sie JavaScript-Democodebeispiele für Azure Cognitive Search, für die das Azure .NET SDK für JavaScript verwendet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: f83767813ea3923d85db2ca3f0164776c610525e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955019"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>JavaScript-Codebeispiele für Azure Cognitive Search

Erfahren Sie mehr über die JavaScript-Codebeispiele, die die Funktionen und den Workflow einer Azure Cognitive Search-Lösung veranschaulichen. In diesen Beispielen wird die [**Azure Cognitive Search-Clientbibliothek**](/javascript/api/overview/azure/search-documents-readme) für das [**Azure SDK für JavaScript**](/azure/developer/javascript/) verwendet, die Sie über die folgenden Links erkunden können.

| Ziel | Link |
|--------|------|
| Paketdownload | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API-Referenz | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API-Testfälle | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Quellcode | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK-Beispiele

Die Codebeispiele vom Azure SDK-Entwicklungsteam veranschaulichen die API-Verwendung. Sie finden diese Beispiele auf GitHub unter [**azure-sdk-for-js/tree/master/sdk/search/search-documents/samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples).

### <a name="javascript-sdk-samples"></a>JavaScript SDK-Beispiele

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Indizes](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Veranschaulicht, wie [Suchindizes](search-what-is-an-index.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden. Diese Beispielkategorie enthält auch ein Beispiel für eine Dienststatistik. |
| [dataSourceConnections (für Indexer)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Veranschaulicht das Erstellen, Aktualisieren, Abrufen, Auflisten und Löschen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Indexer](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Veranschaulicht, wie [Indexer](search-indexer-overview.md) erstellt, aktualisiert, abgerufen, aufgelistet, zurückgesetzt und gelöscht werden.|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Veranschaulicht, wie [Synonymzuordnungen](search-synonyms.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden.  |
| [Abfragen](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Veranschaulicht die Abfrageausführung für einen schreibgeschützten öffentlichen Index, der von Microsoft gehostet wird.  |

### <a name="typescript-samples"></a>TypeScript-Beispiele

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Indizes](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Veranschaulicht, wie [Suchindizes](search-what-is-an-index.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden. Diese Beispielkategorie enthält auch ein Beispiel für eine Dienststatistik. |
| [dataSourceConnections (für Indexer)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Veranschaulicht das Erstellen, Aktualisieren, Abrufen, Auflisten und Löschen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Indexer](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Veranschaulicht, wie [Indexer](search-indexer-overview.md) erstellt, aktualisiert, abgerufen, aufgelistet, zurückgesetzt und gelöscht werden.|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Veranschaulicht, wie [Synonymzuordnungen](search-synonyms.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden.  |
| [Abfragen](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Veranschaulicht die Abfrageausführung für einen schreibgeschützten öffentlichen Index, der von Microsoft gehostet wird.  |

## <a name="doc-samples"></a>Dokumentationsbeispiele

Die Codebeispiele vom Cognitive Search-Team veranschaulichen die Funktionen und Workflows. Auf viele dieser Beispiele wird in Tutorials, Schnellstarts und Anleitungen verwiesen. Sie finden diese Beispiele auf GitHub unter [**Azure-Samples/azure-search-javascript-samples**](https://github.com/Azure-Samples/azure-search-javascript-samples).

| Beispiele | Artikel |
|---------|---------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Quellcode für [Schnellstart: Erstellen eines Suchindex in JavaScript](search-get-started-javascript.md). In diesem Artikel wird der allgemeine Workflow zum Erstellen, Laden und Abfragen eines Suchindex unter Verwendung von Beispieldaten beschrieben. |

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=javascript&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="other-samples"></a>Weitere Beispiele

Die folgenden Beispiele werden ebenfalls vom Cognitive Search-Team veröffentlicht, ohne dass jedoch in der Dokumentation auf sie Bezug genommen wird. Die zugehörigen Infodateien enthalten Anweisungen zu ihrer Verwendung.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | React-Vorlage für Azure Cognitive Search (github.com) |