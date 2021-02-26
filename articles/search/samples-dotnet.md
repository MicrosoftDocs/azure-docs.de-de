---
title: Beispiele für .NET
titleSuffix: Azure Cognitive Search
description: Hier finden Sie C#-Democodebeispiele für Azure Cognitive Search, in denen die .NET-Clientbibliotheken verwendet werden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: cbb84a4934eed4d258cf07772753315785f98019
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218161"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>.NET-Codebeispiele (C#) für Azure Cognitive Search

Hier finden Sie Informationen zu den C#-Codebeispielen, die die Funktionen und den Workflow einer Azure Cognitive Search-Lösung veranschaulichen. In diesen Beispielen wird die [**Azure Cognitive Search-Clientbibliothek**](/dotnet/api/overview/azure/search) für das [**Azure SDK für .NET**](/dotnet/azure/) verwendet, die Sie über die folgenden Links erkunden können.

| Ziel | Link |
|--------|------|
| Paketdownload | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API-Referenz | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API-Testfälle | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Quellcode | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK-Beispiele

Die Codebeispiele vom Azure SDK-Entwicklungsteam veranschaulichen die API-Verwendung. Sie finden diese Beispiele in GitHub unter [**Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/).

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Hello World (synchron)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Veranschaulicht die Clienterstellung, die Authentifizierung und die Behandlung von Fehlern mit synchronen Methoden.|
| [Hello World (asynchron)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Veranschaulicht die Clienterstellung, die Authentifizierung und die Behandlung von Fehlern mit asynchronen Methoden.  |
| [Vorgänge auf Dienstebene](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Veranschaulicht die Erstellung von Indizes, Indexern, Datenquellen, Skillsets und Synonymzuordnungen. In diesem Beispiel wird auch gezeigt, wie Sie Dienststatistiken abrufen und einen Index abfragen.  |
| [Indexvorgänge](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Veranschaulicht das Ausführen einer Aktion für einen vorhandenen Index (in diesem Fall: das Abrufen der Anzahl von im Index gespeicherten Dokumenten).  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Veranschaulicht eine Technik für die Verwendung nicht unterstützter Datentypen.  |
| [Indizieren von Dokumenten (Pushmodell)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Pushmodellindizierung, bei der JSON-Nutzdaten an einen Index in einem Dienst gesendet werden.   |
| [Verschlüsselungsschlüsselbeispiel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Veranschaulicht die Verwendung eines kundenseitig verwalteten Verschlüsselungsschlüssels, um vertrauliche Inhalte noch besser zu schützen.  |

## <a name="doc-samples"></a>Dokumentationsbeispiele

Die Codebeispiele vom Cognitive Search-Team veranschaulichen die Funktionen und Workflows. Auf viele dieser Beispiele wird in Tutorials, Schnellstarts und Anleitungen verwiesen. Sie finden diese Beispiele auf GitHub unter [**Azure-Samples/azure-search-dotnet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) und [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started/).

| Beispiele | Artikel  |
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Quellcode für [Schnellstart: Schnellstart: Erstellen eines Suchindexes mithilfe der Clientbibliothek „Azure.Search.Documents“](search-get-started-dotnet.md) In diesem Artikel wird der allgemeine Workflow zum Erstellen, Laden und Abfragen eines Suchindex unter Verwendung von Beispieldaten beschrieben. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Quellcode für [Verwenden von Azure.Search.Documents in einer in C# geschriebenen .NET-Anwendung](search-howto-dotnet-sdk.md). In diesem Artikel wird der grundlegende Workflow erläutert, jedoch mit mehr Details und einer Diskussion der API-Nutzung.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Quellcode für [Beispiel: Hinzufügen von Synonymen für die kognitive Azure-Suche in C#](search-synonyms-tutorial-sdk.md) enthalten. Synonymlisten werden für die Abfrageerweiterung verwendet und bieten Begriffe für den Abgleich, die für einen Index extern sind. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Quellcode für [Tutorial: Indizieren von Azure SQL-Daten mithilfe des .NET SDK](search-indexer-tutorial.md). In diesem Artikel wird gezeigt, wie Sie einen Azure SQL-Indexer konfigurieren, der über einen Zeitplan, Feldzuordnungen und Parameter verfügt.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Quellcode für [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search](search-security-manage-encryption-keys.md). |
| [Erstellen Ihrer ersten App in C#](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Quellcode für [Tutorial: Erstellen Ihrer ersten Such-App mit dem .NET SDK](tutorial-csharp-create-first-app.md). Bei den meisten Beispielen handelt es sich um Konsolenanwendungen. In diesem MVC-Beispiel wird dagegen eine Webseite für den exemplarischen Hotelindex verwendet, um grundlegende Such-, Paginierungs- und AutoVervollständigen-Funktionen sowie empfohlene Abfragen, Facetten und Filter zu veranschaulichen. |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Quellcode für [Tutorial: Indizieren von mehreren Datenquellen mithilfe des .NET SDK](tutorial-multiple-data-sources.md) |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Quellcode für [Tutorial: Optimieren der Indizierung mit der Push-API](tutorial-optimize-indexing-push-api.md)  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Quellcode für [Tutorial: Durch KI generierter durchsuchbarer Inhalt aus Azure-Blobs mit dem .NET SDK](cognitive-search-tutorial-blob-dotnet.md)  |

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?languages=csharp&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="other-samples"></a>Weitere Beispiele

Die folgenden Beispiele werden ebenfalls vom Cognitive Search-Team veröffentlicht, ohne dass jedoch in der Dokumentation auf sie Bezug genommen wird. Die zugehörigen Infodateien enthalten Anweisungen zu ihrer Verwendung.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Quellcode für nutzbare benutzerdefinierte Skills, die Sie in Ihre eigenen Lösungen integrieren können.  |
| [Solution Accelerator für Knowledge Mining](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Umfasst Vorlagen, Unterstützungsdateien und Analyseberichte, die Sie bei der Erstellung eines Prototyps einer End-to-End-Lösung für Knowledge Mining unterstützen.  |
| [Repository für Covid-19-Such-App](https://github.com/liamca/covid19search) | Quellcoderepository für die auf Cognitive Search basierende [Covid-19-Such-App](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Weitere Informationen zur JFK-Lösung finden Sie [hier](https://www.microsoft.com/ai/ai-lab-jfk-files). |
| [Beschleuniger für Suche und QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Eine [Lösung](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381), die die Leistungsfähigkeit von Suche und QnA Maker kombiniert. Weitere Informationen finden Sie auf der Live-[Demowebsite](https://aka.ms/qnaWithAzureSearchDemo). |