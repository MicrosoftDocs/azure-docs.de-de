---
title: 'Azure Cosmos DB: Java-API, SDK und Ressourcen für BulkExecutor'
description: Wichtige Informationen zur Java-API und zum SDK für BulkExecutor, einschließlich Veröffentlichungsdaten, Deaktivierungsdaten und Änderungen an den einzelnen Versionen des Azure Cosmos DB-BulkExecutor-Java-SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6f5993826c7d8c39f4e062c0a84ffd95eaf9cf2d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098602"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-BulkExecutor-Bibliothek: Informationen zum Download
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Beschreibung**|Die BulkExecutor-Bibliothek ermöglicht Clientanwendungen die Ausführung von Massenvorgängen in Azure Cosmos DB-Konten. Die BulkExecutor-Bibliothek stellt die Namespaces „BulkImport“ und „BulkUpdate“ bereit. Das BulkImport-Modul kann Dokumente auf optimierte Weise per Massenimport erfassen, sodass der für eine Sammlung bereitgestellte Durchsatz maximal genutzt wird. Das BulkUpdate-Modul kann vorhandene Daten in Azure Cosmos-Containern per Massenvorgang in Form von Patches aktualisieren.|
|**SDK-Download**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Bulk Executor-Bibliothek auf GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-Dokumentation**| [Java-API-Referenzdokumentation](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Erste Schritte**|[Erste Schritte mit dem Java SDK für die BulkExecutor-Bibliothek](bulk-executor-java.md)|
|**Unterstützte Mindestlaufzeit**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Versionshinweise

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Korrektur für „DocumentAnalyzer.java“, damit Schlüsselwerte geschachtelter Partitionen ordnungsgemäß aus JSON-Code extrahiert werden.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Funktionen in Massenlöschungsvorgängen hinzugefügt, mit denen Wiederholungsversuche für bestimmte Fehler ausgeführt werden und die auch eine Liste der Fehler, die wiederholt werden könnten, an den Benutzer zurückgeben.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Update für Cosmos SDK Version 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Korrektur für mergeAll zum Fortsetzen nach „ID“ und Partitionsschlüsselwert, sodass alle gepatchten Dokumenteigenschaften, die nach „ID“ und dem Partitionsschlüsselwert stehen, der aktualisierten Elementliste hinzugefügt werden.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Startgrad der Parallelität auf 1 aktualisiert und Debugprotokolle für minibatch hinzugefügt.