---
title: Beispiele für Java
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Java-Democodebeispiele für Azure Cognitive Search, für die das Azure .NET SDK für Java verwendet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 10dff18f7b9db7273fcd6ec92bcca5970bb83b08
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510368"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Java-Codebeispiele für Azure Cognitive Search

Hier finden Sie Informationen zu den Java-Codebeispielen, die die Features und Funktionen von Azure Cognitive Search veranschaulichen. Primäre Repositorys:

| Repository | BESCHREIBUNG |
|------------|-------------|
| [azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Vom Azure SDK-Team erstellte Beispiele, die in der Clientbibliothek „Azure.Search.Documents“ des SDK enthalten sind. Anhand von [Komponententests](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) für die Clientbibliothek können Sie sich außerdem ansehen, wie verschiedene APIs aufgerufen werden. |
| [Azure-Samples/azure-search-java-samples](https://github.com/Azure-Samples/azure-search-java-samples) | Codebeispiele zu Artikeln mit Anleitungen. **Die Beispiele in diesem Repository wurden noch nicht für das Azure SDK für Java aktualisiert**. Derzeit werden in diesen Beispielen REST-APIs in Java-Code aufgerufen.|

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=java&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="java-sdk-samples"></a>Java SDK-Beispiele

Das Azure SDK für Java enthält zahlreiche Beispiele und eine [Seite „Erste Schritte“](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) zur Paketinstallation. Auf der Seite ist außerdem eine Reihe von Beispielen aufgeführt. Einige der gängigeren Vorgänge sind unten aufgeführt.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Suchindexerstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Veranschaulicht das Erstellen von [Suchindizes](search-what-is-an-index.md). |
| [Synonymerstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Veranschaulicht das Erstellen von [Synonymzuordnungen](search-synonyms.md).  |
| [Suchindexererstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Veranschaulicht das Erstellen von [Indexern](search-indexer-overview.md). |
| [Datenquellenerstellung für Suchindexer](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Veranschaulicht das Erstellen von Indexerdatenquellen, die für die indexerbasierte Indizierung [unterstützter Azure-Datenquellen](search-indexer-overview.md#supported-data-sources) erforderlich sind. |
| [Skillseterstellung](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Veranschaulicht, wie [Skillsets](cognitive-search-working-with-skillsets.md) erstellt werden, bei denen es sich um angefügte Indexer handelt und die während der Indizierung eine KI-basierte Anreicherung durchführen. |
| [Laden von Dokumenten](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Veranschaulicht das Hochladen oder Zusammenführen von Dokumenten in einen Index in einem [Datenimport](search-what-is-data-import.md)-Vorgang. |
| [Abfragesyntax](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Veranschaulicht das Einrichten einer [einfachen Abfrage](search-query-overview.md). |

## <a name="documentation-samples"></a>Dokumentationsbeispiele

Zu den folgenden Beispielen ist ein entsprechender Artikel in der [Dokumentation zu Azure Cognitive Search](./index.yml) vorhanden.

| Beispiele | BESCHREIBUNG | 
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Quellcode für [Schnellstart: Erstellen eines Suchindex in Java](search-get-started-java.md). In diesem Beispiel werden die REST-APIs aufgerufen. |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Veranschaulicht einen Azure Cosmos DB-Indexer in Java. In diesem Beispiel werden die REST-APIs aufgerufen. |