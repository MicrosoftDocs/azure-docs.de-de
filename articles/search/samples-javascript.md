---
title: JavaScript-Beispiele
titleSuffix: Azure Cognitive Search
description: Hier finden Sie JavaScript-Democodebeispiele für Azure Cognitive Search, für die das Azure .NET SDK für JavaScript verwendet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 09a9ca2ad1b1f1e5578ecc5d35a85c81b32a5b1a
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511745"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>JavaScript-Codebeispiele für Azure Cognitive Search

Hier finden Sie Informationen zu den JavaScript-Codebeispielen, die die Features und Funktionen von Azure Cognitive Search veranschaulichen. Primäre Repositorys:

| Repository | Beschreibung |
|------------|-------------|
| [azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Vom Azure SDK-Team erstellte Beispiele, die in der Clientbibliothek „Azure.Search.Documents“ des SDK enthalten sind. Anhand von [Komponententests](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) für die Clientbibliothek können Sie sich außerdem ansehen, wie verschiedene APIs aufgerufen werden. |
| [Azure-Samples/azure-search-javascript-samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Codebeispiele zu Artikeln mit Anleitungen, einschließlich [Schnellstart: Erstellen eines Suchindex in JavaScript](search-get-started-javascript.md).|

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=javascript&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="javascript-sdk-samples"></a>JavaScript SDK-Beispiele

Das Azure SDK für Java enthält zahlreiche Beispiele und eine [Seite „Erste Schritte“](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) zur Paketinstallation, zur Einrichtung des Clients und zur Problembehandlung. Auf der Seite werden auch die folgenden Beispielkategorien beschrieben, die hier ebenfalls aufgeführt sind.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Indizes](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Veranschaulicht, wie [Suchindizes](search-what-is-an-index.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden. Diese Beispielkategorie enthält auch ein Beispiel für eine Dienststatistik. |
| [dataSourceConnections (für Indexer)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Veranschaulicht das Erstellen, Aktualisieren, Abrufen, Auflisten und Löschen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Indexer](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Veranschaulicht, wie [Indexer](search-indexer-overview.md) erstellt, aktualisiert, abgerufen, aufgelistet und zurückgesetzt werden.|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Veranschaulicht, wie [Synonymzuordnungen](search-synonyms.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden.  |
| [Abfragen](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Veranschaulicht die Abfrageausführung für einen schreibgeschützten öffentlichen Index, der von Microsoft gehostet wird.  |

## <a name="typescript-samples"></a>TypeScript-Beispiele

Das SDK bietet auch TypeScript-Beispiele, die hier aufgeführt sind.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Indizes](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Veranschaulicht, wie [Suchindizes](search-what-is-an-index.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden. Diese Beispielkategorie enthält auch ein Beispiel für eine Dienststatistik. |
| [dataSourceConnections (für Indexer)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Veranschaulicht das Erstellen, Aktualisieren, Abrufen, Auflisten und Löschen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Indexer](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Veranschaulicht, wie [Indexer](search-indexer-overview.md) erstellt, aktualisiert, abgerufen, aufgelistet und zurückgesetzt werden.|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Veranschaulicht, wie [Synonymzuordnungen](search-synonyms.md) erstellt, aktualisiert, abgerufen, aufgelistet und gelöscht werden.  |
| [Abfragen](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Veranschaulicht die Abfrageausführung für einen schreibgeschützten öffentlichen Index, der von Microsoft gehostet wird.  |

## <a name="documentation-samples"></a>Dokumentationsbeispiele

Zu den folgenden Beispielen ist ein entsprechender Artikel in der [Dokumentation zu Azure Cognitive Search](./index.yml) vorhanden.

| Beispiele | BESCHREIBUNG | 
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Quellcode für [Schnellstart: Erstellen eines Suchindex in JavaScript](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Eigenständige Beispiele

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | React-Vorlage für Azure Cognitive Search (github.com) |