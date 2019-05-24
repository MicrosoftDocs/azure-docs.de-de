---
title: Verwenden des Azure Cosmos DB-Änderungsfeeds in Azure Functions
description: Verwenden des Azure Cosmos DB-Änderungsfeeds in Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 08429ca76823b9e6c80a197cc390a5964c4198e6
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969007"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Serverlose, ereignisbasierte Architekturen mit Azure Cosmos DB und Azure Functions

Azure Functions bietet die einfachste Möglichkeit zum Herstellen einer Verbindung mit dem [Änderungsfeed](change-feed.md). Sie können kleine reaktive Azure-Funktionen erstellen, die automatisch bei jedem neuen Ereignis im Änderungsfeed Ihres Azure Cosmos-Containers ausgelöst werden.

![Serverlose, ereignisbasierte Funktionen mit dem Azure Cosmos DB-Trigger](./media/change-feed-functions/functions.png)

Mit dem [Azure Cosmos DB-Trigger](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) können Sie die Skalierung des [Änderungsfeedprozessors](./change-feed-processor.md) sowie die Funktionen zur zuverlässigen Erkennung nutzen, ohne eine [Workerinfrastruktur](./change-feed-processor.md#implementing-the-change-feed-processor-library) verwalten zu müssen. Sie können sich ganz auf die Logik Ihrer Azure-Funktion konzentrieren, ohne sich um die Pipeline für die Ereignisquellen kümmern zu müssen. Sie können den Trigger auch mit anderen [Azure Functions-Bindungen](../azure-functions/functions-triggers-bindings.md#supported-bindings) kombinieren.

> [!NOTE]
> Derzeit wird der Azure Cosmos DB-Trigger nur für die Verwendung mit der Kern-API (SQL) unterstützt.

## <a name="requirements"></a>Requirements (Anforderungen)

Um einen serverlosen, ereignisbasierten Flow zu implementieren, ist Folgendes erforderlich:

* **Überwachter Container:** Dies ist der überwachte Azure Cosmos-Container, der die Daten enthält, aus denen der Änderungsfeed generiert wird. Alle Einfügungen im und Änderungen (z. B. CRUD) am überwachten Container werden im Änderungsfeed des Containers wiedergegeben.
* **Leasecontainer:** Der Leasecontainer verwaltet den Zustand für mehrere und dynamische serverlose Azure Functions-Instanzen und ermöglicht die dynamische Skalierung. Dieser Leasecontainer kann manuell oder automatisch vom Azure Cosmos DB-Trigger erstellt werden. Für das automatische Erstellen des Leasecontainers legen Sie das *CreateLeaseCollectionIfNotExists*-Flag in der [Konfiguration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) fest. Partitionierte Leasecontainer benötigen die Partitionsschlüsseldefinition `/id`.

## <a name="create-your-azure-cosmos-db-trigger"></a>Erstellen eines Azure Cosmos DB-Triggers

Das Erstellen von Azure-Funktionen mit einem Azure Cosmos DB-Trigger wird jetzt in allen Azure Functions-IDE- und -CLI-Integrationen unterstützt:

* [Visual Studio-Erweiterung](../azure-functions/functions-develop-vs.md) für Visual Studio-Benutzer
* [Visual Studio-Kernerweiterung](https://code.visualstudio.com/tutorials/functions-extension/create-function) für Visual Studio Code-Benutzer
* Und schließlich [Core-CLI-Tools](../azure-functions/functions-run-local.md#create-func) für eine plattformübergreifende Umgebung, die unabhängig von der IDE ist

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Lokales Ausführen des Azure Cosmos DB-Triggers

Sie können Ihre [Azure-Funktion lokal](../azure-functions/functions-develop-local.md) mit dem [Azure Cosmos DB-Emulator](./local-emulator.md) ausführen, um Ihre serverlosen, ereignisbasierten Flows ohne ein Azure-Abonnement oder zusätzliche Kosten zu erstellen und zu entwickeln.

Wenn Sie Liveszenarien in der Cloud testen möchten, können Sie [Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/), ohne eine Kreditkarte oder ein Azure-Abonnement zu benötigen.

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über den Änderungsfeed:

* [Übersicht über Änderungsfeeds](change-feed.md)
* [Möglichkeiten zum Lesen von Änderungsfeeds](read-change-feed.md)
* [Verwenden der Änderungsfeed-Verarbeitungsbibliothek](change-feed-processor.md)
* [Arbeiten mit der Änderungsfeed-Verarbeitungsbibliothek](change-feed-processor.md)
* [Serverloses Datenbankcomputing mit Azure Cosmos DB und Azure Functions](serverless-computing-database.md)
