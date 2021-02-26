---
title: REST-Beispiele
titleSuffix: Azure Cognitive Search
description: Suchen nach REST-Codebeispielen für Azure Cognitive Search zu Demozwecken, die die Search- oder Management-REST-APIs verwenden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956442"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>REST-Codebeispiele für Azure Cognitive Search

Hier finden Sie Informationen zu den REST-API-Beispielen, die die Funktionen und den Workflow einer Azure Cognitive Search-Lösung veranschaulichen. Diese Beispiele verwenden die [**Search-REST-APIs**](/rest/api/searchservice).

REST ist die maßgebliche Programmierschnittstelle für Azure Cognitive Search, und alle Operationen, die programmatisch aufgerufen werden können, sind zuerst in REST und dann in SDKs verfügbar. Aus diesem Grund werden für die meisten Beispiele in der Dokumentation die REST-APIs verwendet, um wichtige Konzepte zu demonstrieren oder zu erklären.

REST-Beispiele werden normalerweise auf Postman entwickelt und getestet, aber Sie können jeden Client verwenden, der HTTP-Aufrufe unterstützt:

+ Beginnen Sie mit [Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs](search-get-started-rest.md). Hier erhalten Sie Hilfe beim Formulieren von HTTP-Aufrufen.
+ Testen Sie die [Visual Studio Code-Erweiterung für Azure Cognitive Search](search-get-started-vs-code.md), die sich derzeit in der Vorschau befindet, wenn Sie Visual Studio Code verwenden.

## <a name="doc-samples"></a>Dokumentationsbeispiele

Die Codebeispiele vom Cognitive Search-Team veranschaulichen die Funktionen und Workflows. Auf viele dieser Beispiele wird in Tutorials, Schnellstarts und Anleitungen verwiesen. Sie finden diese Beispiele in GitHub unter [**Azure-Samples/azure-search-postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples).

| Beispiele | Artikel |
|---------|---------|
| [Schnellstart](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Quellcode für [Schnellstart: Erstellen eines Suchindex mithilfe von REST-APIs](search-get-started-rest.md). In diesem Artikel wird der allgemeine Workflow zum Erstellen, Laden und Abfragen eines Suchindex unter Verwendung von Beispieldaten beschrieben. |
| [Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Quellcode für [Tutorial: Verwenden von REST und KI zum Generieren von durchsuchbarem Inhalt über Azure-Blobs](cognitive-search-tutorial-blob.md). In diesem Artikel erfahren Sie, wie Sie ein Skillset erstellen, das über Azure-Blobs iteriert, um Informationen zu extrahieren und eine Struktur abzuleiten.|
| [Debugsitzungen](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Quellcode für [Tutorial: Diagnostizieren, Reparieren und Committen von Änderungen an Ihrem Skillset](cognitive-search-tutorial-debug-sessions.md). In diesem Artikel wird gezeigt, wie Sie eine Skillset-Debugsitzung im Azure-Portal verwenden. REST wird zum Erstellen der während des Debuggens verwendeten Objekte verwendet.|
| [Benutzerdefinierte Analysetools](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Quellcode für [Tutorial: Erstellen eines benutzerdefinierten Analysetools für Telefonnummern](tutorial-create-custom-analyzer.md). In diesem Artikel wird erläutert, wie Analysen verwendet werden, um Muster und Sonderzeichen in durchsuchbarem Inhalt beizubehalten.|
| [Wissensspeicher](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Quellcode für [Erstellen eines Wissensspeichers mithilfe von REST und Postman](knowledge-store-create-rest.md). In diesem Artikel werden die erforderlichen Schritte zum Auffüllen eines Wissensspeichers erläutert, der für Wissensgewinnungsworkflows (Knowledge Mining) verwendet wird. |
| [Projektionen](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Quellcode für [Strukturieren und Exportieren von Anreicherungen](knowledge-store-projections-examples.md). In diesem Artikel wird erläutert, wie die physischen Datenstrukturen in einem Wissensspeicher festgelegt werden.|
| [Indizieren verschlüsselter Blobs](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Quellcode für [Indizieren verschlüsselter Blobs mithilfe von Blobindexern und Skillsets](search-howto-index-encrypted-blobs.md). In diesem Artikel wird beschrieben, wie Dokumente in Azure Blob Storage indiziert werden, die zuvor mit Azure Key Vault verschlüsselt wurden. |

> [!Tip]
> Testen Sie den [Beispielbrowser](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search), um GitHub nach Microsoft-Codebeispielen zu durchsuchen (gefiltert nach Produkt, Dienst und Sprache).

## <a name="other-samples"></a>Weitere Beispiele

Die folgenden Beispiele werden ebenfalls vom Cognitive Search-Team veröffentlicht, ohne dass jedoch in der Dokumentation auf sie Bezug genommen wird. Die zugehörigen Infodateien enthalten Anweisungen zu ihrer Verwendung.

| Beispiele | BESCHREIBUNG |
|---------|-------------|
| [Abfragebeispiele](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Postman-Sammlungen, die die verschiedenen Abfragetechniken veranschaulichen, wie z. B. Fuzzysuche, RegEx und Platzhaltersuche, AutoVervollständigen usw. |