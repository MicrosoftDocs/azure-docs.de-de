---
title: 'Azure Cosmos DB: BulkExecutor – .NET-API, SDK und Ressourcen'
description: Wichtige Informationen zur .NET-API und zum SDK für BulkExecutor, einschließlich Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des .NET SDK für Azure Cosmos DB BulkExecutor.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 21b3a5024b7b38f3f284b7213853c7089838930f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097208"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET-BulkExecutor-Bibliothek: Informationen zum Download 
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
| **Beschreibung**| Der .NET-Bulk Executor ermöglicht Clientanwendungen die Ausführung von Massenvorgängen für Azure Cosmos DB-Konten. Diese Bibliothek stellt die Namespaces „BulkImport“, „BulkUpdate“ und „BulkDelete“ bereit. Das BulkImport-Modul kann Dokumente auf optimierte Weise per Massenimport erfassen, sodass der für eine Sammlung bereitgestellte Durchsatz maximal genutzt wird. Das BulkUpdate-Modul kann vorhandene Daten in Azure Cosmos-Containern per Massenvorgang in Form von Patches aktualisieren. Das BulkDelete-Modul kann Dokumente auf optimierte Weise per Massenlöschung entfernen, sodass der für eine Sammlung bereitgestellte Durchsatz maximal genutzt wird.|
|**SDK-Download**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bulk Executor-Bibliothek auf GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-Dokumentation**|[.NET API-Referenzdokumentation](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?preserve-view=true&view=azure-dotnet)|
|**Erste Schritte**|[Erste Schritte mit dem .NET SDK für die BulkExecutor-Bibliothek](bulk-executor-dot-net.md)|
| **Aktuelles unterstütztes Framework**| Microsoft .NET Framework 4.5.2, 4.6.1 und .NET Standard 2.0 |

> [!NOTE]
> Wenn Sie Bulk Executor verwenden, sehen Sie sich die neueste 3.x-Version des [.NET SDKs](tutorial-sql-api-dotnet-bulk-import.md) an, da Bulk Executor bei dieser Version in die SDK integriert ist. 

## <a name="release-notes"></a>Versionshinweise

### <a name="241-preview"></a><a name="2.4.1-preview"></a>2.4.1-preview

* „TotalElapsedTime“ in der Antwort von „BulkDelete“ wurde zur korrekten Messung der Gesamtzeit (einschließlich Wiederholungen) korrigiert.

### <a name="240-preview"></a><a name="2.4.0-preview"></a>2.4.0-preview

* SDK-Abhängigkeit wurde in > = 2.5.1 geändert.

### <a name="230-preview2"></a><a name="2.3.0-preview2"></a>2.3.0-preview2

* Unterstützung für Graph-BulkExecutor wurde hinzugefügt, damit TTL für Scheitelpunkte und Kanten akzeptiert wird

### <a name="220-preview2"></a><a name="2.2.0-preview2"></a>2.2.0-preview2

* Es wurde ein Problem behoben, bei dem es zu Ausnahmen während der elastischen Skalierung von Azure Cosmos DB gekommen ist, wenn die Ausführung im Modus „Gateway“ erfolgt ist. Aufgrund dieser Fehlerbehebung entspricht die Funktionalität Version 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"></a>2.1.0-preview2

* Unterstützung des BulkDelete-Vorgangs wurde hinzugefügt, damit SQL-API-Konten Tupel aus Partitionsschlüsseln und Dokument-IDs für das Löschen akzeptieren. Aufgrund dieser Änderung entspricht die Funktionalität Version 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"></a>2.0.0-preview2

* Enthält nun MongoBulkExecutor zur Unterstützung von .NET Standard 2.0. Dieses Feature stellt funktionale Gleichheit mit dem Release 1.3.0 her und unterstützt als Zielframework zusätzlich auch .NET Standard 2.0.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview

* .NET Standard 2.0 wurde als unterstütztes Zielframework hinzugefügt, damit die Bulk Executor-Bibliothek mit .NET Core-Anwendungen verwendet werden kann.

### <a name="189"></a><a name="1.8.9"></a>1.8.9

* Es wurde ein Problem mit BulkDeleteAsync behoben, wenn Werte mit Anführungszeichen in Escapezeichen als Eingabeparameter übergeben wurden.

### <a name="188"></a><a name="1.8.8"></a>1.8.8

* Es wurde ein Problem in MongoBulkExecutor behoben, durch das die Dokumentgröße unerwartet vergrößert wurde, indem Auffüllungen hinzugefügt wurden, und in einigen Fällen die maximal zulässige Dokumentgröße überschritten wurde.

### <a name="187"></a><a name="1.8.7"></a>1.8.7

* Es wurde ein Problem mit „BulkDeleteAsync“ behoben, das auftrat, wenn die Sammlung geschachtelte Partitionsschlüsselpfade aufweist.

### <a name="186"></a><a name="1.8.6"></a>1.8.6

* „MongoBulkExecutor“ implementiert nun „IDisposable“, und es wird erwartet, dass das Element nach der Verwendung entfernt wird.

### <a name="185"></a><a name="1.8.5"></a>1.8.5

* Die Sperre für die SDK-Version wurde entfernt. Das Paket ist nun abhängig von SDK-Version 2.5.1 oder höher.

### <a name="184"></a><a name="1.8.4"></a>1.8.4

* Die Behandlung von Bezeichnern beim Aufrufen von „BulkImport“ mit einer Liste von POCO-Objekten mit numerischen Werten wurde korrigiert.

### <a name="183"></a><a name="1.8.3"></a>1.8.3

* „TotalElapsedTime“ in der Antwort von „BulkDelete“ wurde zur korrekten Messung der Gesamtzeit (einschließlich Wiederholungen) korrigiert.

### <a name="182"></a><a name="1.8.2"></a>1.8.2

* Eine hohe CPU-Auslastung in bestimmten Szenarien wurde behoben.
* Die Ablaufverfolgung verwendet jetzt TraceSource. Benutzer können Listener für die `BulkExecutorTrace`-Quelle definieren.
* Ein seltenes Szenario, bei dem der Versand von Dokumenten mit ungefähr 2 MB blockiert wurde, wurde behoben.

### <a name="160"></a><a name="1.6.0"></a>1.6.0

* BulkExecutor wurde aktualisiert, damit die aktuelle Version des Azure Cosmos DB .NET SDK (2.4.0) verwendet wird.

### <a name="150"></a><a name="1.5.0"></a>1.5.0

* Unterstützung für Graph-BulkExecutor wurde hinzugefügt, damit TTL für Scheitelpunkte und Kanten akzeptiert wird

### <a name="141"></a><a name="1.4.1"></a>1.4.1

* Es wurde ein Problem behoben, bei dem es zu Ausnahmen während der elastischen Skalierung von Azure Cosmos DB gekommen ist, wenn die Ausführung im Modus „Gateway“ erfolgt ist.

### <a name="140"></a><a name="1.4.0"></a>1.4.0

* Unterstützung des BulkDelete-Vorgangs wurde hinzugefügt, damit SQL-API-Konten Tupel aus Partitionsschlüsseln und Dokument-IDs für das Löschen akzeptieren.

### <a name="130"></a><a name="1.3.0"></a>1.3.0

* Ein Problem wurde behoben, durch das ein Formatierungsproblem in dem von Bulk Executor verwendeten Benutzer-Agent verursacht wurde.

### <a name="120"></a><a name="1.2.0"></a>1.2.0

* Es wurden Verbesserungen an den Import- und Update-APIs von Bulk Executor vorgenommen, um transparent die flexible Skalierung von Cosmos-Containern bei Überschreiten der aktuellen Speicherkapazität zu ermöglichen, ohne dass Ausnahmen ausgelöst werden.

### <a name="112"></a><a name="1.1.2"></a>1.1.2

* Aktualisierung der DocumentDB .NET SDK-Abhängigkeit auf Version 2.1.3.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Ein Problem wurde behoben, durch das Bulk Executor beim Importieren in feste Sammlungen JSRT-Fehler auslöste.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Unterstützung für BulkDelete-Vorgang für die SQL-API von Azure Cosmos DB-Konten.
* Unterstützung für den BulkImport-Vorgang für Konten mit der MongoDB-API von Azure Cosmos DB.
* Aktualisierung der DocumentDB .NET SDK-Abhängigkeit auf Version 2.0.0. 

### <a name="102"></a><a name="1.0.2"></a>1.0.2

* Unterstützung für BulkImport-Vorgang für die Gremlin-API von Azure Cosmos DB-Konten.

### <a name="101"></a><a name="1.0.1"></a>1.0.1

* Kleinere Fehlerbehebung am BulkImport-Vorgang für die SQL-API von Azure Cosmos DB-Konten.

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Unterstützung für BulkImport- und Bulkupdate-Vorgänge für die SQL-API von Azure Cosmos DB-Konten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur BulkExecutor-Java-Bibliothek finden Sie im folgenden Artikel:

[Java-BulkExecutor-Bibliothek: Informationen zum Download](sql-api-sdk-bulk-executor-java.md)