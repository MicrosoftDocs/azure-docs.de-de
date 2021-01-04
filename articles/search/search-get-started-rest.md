---
title: 'Schnellstart: Erstellen eines Suchindex mithilfe von REST-APIs'
titleSuffix: Azure Cognitive Search
description: In diesem REST-API-Schnellstart erfahren Sie, wie Sie mithilfe von Postman oder Visual Studio Code die Azure Cognitive Search-REST-APIs aufrufen.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711334"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Schnellstart: Erstellen eines Azure Cognitive Search-Index mithilfe von REST-APIs

In diesem Artikel wird erläutert, wie Sie mithilfe der [Azure Cognitive Search REST-APIs](/rest/api/searchservice) und eines API-Clients zum Senden und Empfangen von Anforderungen interaktiv REST-API-Anforderungen formulieren. Mit einem API-Client und dieser Anleitung können Sie vor dem Schreiben von Code Anforderungen senden und Antworten anzeigen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

Nun wissen Sie, wie Sie grundlegende Aufgaben ausführen, und können mit weiteren REST-API-Aufrufen fortfahren, um sich komplexere Features wie Indexer anzusehen oder [eine Anreicherungspipeline einzurichten](cognitive-search-tutorial-blob.md), die der Indizierung Inhaltstransformationen hinzufügt. Als nächsten Schritt empfehlen wir den folgenden Link:

> [!div class="nextstepaction"]
> [Tutorial: Verwenden von REST und KI zum Generieren von durchsuchbarem Inhalt über Azure-Blobs](cognitive-search-tutorial-blob.md)