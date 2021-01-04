---
title: Beispiele für .NET
titleSuffix: Azure Cognitive Search
description: Hier finden Sie C#-Democodebeispiele für Azure Cognitive Search, in denen die .NET-Clientbibliotheken verwendet werden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: ab6408621616a4be62631391456f73e90fced752
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498998"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>.NET-Codebeispiele (C#) für Azure Cognitive Search

Hier finden Sie Informationen zu den C#-Codebeispielen, die die Features und Funktionen von Azure Cognitive Search veranschaulichen. Primäre Repositorys:

| Repository | BESCHREIBUNG |
|------------|-------------|
| [azure-sdk-for-net/sdk/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Vom Azure SDK-Team erstellte Beispiele, die in der Clientbibliothek „Azure.Search.Documents“ des SDK enthalten sind. Anhand von [Komponententests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) für die Clientbibliothek können Sie sich außerdem ansehen, wie verschiedene APIs aufgerufen werden. |
| [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Begleitende Beispiele für Anleitungsartikel in der Dokumentation (einschließlich [Verwenden von Azure.Search.Documents in einer in C# geschriebenen .NET-Anwendung](search-howto-dotnet-sdk.md)).|
| [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) | Begleitende Beispiele für Schnellstartanleitungen und Tutorials in der Dokumentation.|

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=csharp&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="net-sdk-samples"></a>.NET SDK-Beispiele

Das Azure SDK für .NET enthält zahlreiche Beispiele sowie [Informationen zu den Beispielen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md), in denen diese jeweils beschrieben werden. Im Anschluss finden Sie die entsprechende Liste:

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Hello World (synchron)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Veranschaulicht die Clienterstellung, die Authentifizierung und die Behandlung von Fehlern mit synchronen Methoden.|
| [Hello World (asynchron)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Veranschaulicht die Clienterstellung, die Authentifizierung und die Behandlung von Fehlern mit asynchronen Methoden.  |
| [Vorgänge auf Dienstebene](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Veranschaulicht die Erstellung von Indizes, Indexern, Datenquellen, Skillsets und Synonymzuordnungen. In diesem Beispiel wird auch gezeigt, wie Sie Dienststatistiken abrufen und einen Index abfragen.  |
| [Indexvorgänge](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Veranschaulicht das Ausführen einer Aktion für einen vorhandenen Index (in diesem Fall: das Abrufen der Anzahl von im Index gespeicherten Dokumenten).  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Veranschaulicht eine Technik für die Verwendung nicht unterstützter Datentypen.  |
| [Indizieren von Dokumenten (Pushmodell)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Pushmodellindizierung, bei der JSON-Nutzdaten an einen Index in einem Dienst gesendet werden.   |
| [Verschlüsselungsschlüsselbeispiel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Veranschaulicht die Verwendung eines kundenseitig verwalteten Verschlüsselungsschlüssels, um vertrauliche Inhalte noch besser zu schützen.  |

## <a name="documentation-samples"></a>Dokumentationsbeispiele

Für die folgenden Beispiele ist ein entsprechender Artikel in der [Dokumentation zu Azure Cognitive Search](./index.yml) enthalten.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Quellcode für [Schnellstart: Schnellstart: Erstellen eines Suchindexes mithilfe der Clientbibliothek „Azure.Search.Documents“](search-get-started-dotnet.md)  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Quellcode für [Verwenden von Azure.Search.Documents in einer in C# geschriebenen .NET-Anwendung](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Synonymlisten werden für die Abfrageerweiterung verwendet und bieten Begriffe für den Abgleich, die für einen Index extern sind. Dieses Beispiel ist in [Beispiel: Hinzufügen von Synonymen für die kognitive Azure-Suche in C#](search-synonyms-tutorial-sdk.md) enthalten. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Quellcode hinter indexerbezogenen Codeausschnitten in verschiedenen Artikeln. In diesem Beispiel wird gezeigt, wie Sie einen Indexer konfigurieren, der über einen Zeitplan, Feldzuordnungen und Parameter verfügt.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Quellcode für [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search](search-security-manage-encryption-keys.md) |
| [Erstellen Ihrer ersten App in C#](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Quellcode für [Tutorial: Erstellen Ihrer ersten Such-App mit dem .NET SDK](tutorial-csharp-create-first-app.md). Bei den meisten Beispielen handelt es sich um Konsolenanwendungen. In diesem MVC-Beispiel wird dagegen eine Webseite für den exemplarischen Hotelindex verwendet, um grundlegende Such-, Paginierungs- und AutoVervollständigen-Funktionen sowie empfohlene Abfragen, Facetten und Filter zu veranschaulichen. |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Quellcode für [Tutorial: Indizieren von mehreren Datenquellen mithilfe des .NET SDK](tutorial-multiple-data-sources.md) |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Quellcode für [Tutorial: Optimieren der Indizierung mit der Push-API](tutorial-optimize-indexing-push-api.md)  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Quellcode für [Tutorial: Durch KI generierter durchsuchbarer Inhalt aus Azure-Blobs mit dem .NET SDK](cognitive-search-tutorial-blob-dotnet.md)  |

## <a name="standalone-samples-and-solutions"></a>Eigenständige Beispiele und Lösungen

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Quellcode für nutzbare benutzerdefinierte Skills, die Sie in Ihre eigenen Lösungen integrieren können.  |
| [Solution Accelerator für Knowledge Mining](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Umfasst Vorlagen, Unterstützungsdateien und Analyseberichte, die Sie bei der Erstellung eines Prototyps einer End-to-End-Lösung für Knowledge Mining unterstützen.  |
| [Repository für Covid-19-Such-App](https://github.com/liamca/covid19search) | Quellcoderepository für die auf Cognitive Search basierende [Covid-19-Such-App](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Weitere Informationen zur JFK-Lösung finden Sie [hier](https://www.microsoft.com/ai/ai-lab-jfk-files). |