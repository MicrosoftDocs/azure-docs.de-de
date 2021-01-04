---
title: Migrieren von der Bulk Executor-Bibliothek zur Unterstützung von Massenvorgängen im .NET SDK V3 von Azure Cosmos DB
description: Erfahren Sie, wie Sie Ihre Anwendung von der Verwendung der Bulk Executor-Bibliothek zur Unterstützung von Massenvorgängen des Azure Cosmos DB SDK V3 migrieren.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 24d6b475964e4bf7745495e9c41d0e89bb76f7e9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341283"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrieren von der Bulk Executor-Bibliothek zur Unterstützung von Massenvorgängen im .NET SDK V3 von Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden die erforderlichen Schritte zum Migrieren eines vorhandenen Anwendungscodes, der die [Bulk Executor-Bibliothek von .NET](bulk-executor-dot-net.md) verwendet, zur [Unterstützung von Massenvorgängen](tutorial-sql-api-dotnet-bulk-import.md) in der neuesten Version des .NET SDK beschrieben.

## <a name="enable-bulk-support"></a>Aktivieren der Unterstützung von Massenvorgängen

Sie aktivieren die Unterstützung von Massenvorgängen für die `CosmosClient`-Instanz über die [AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)-Konfiguration:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Erstellen von Aufgaben für jeden Vorgang

Für die Unterstützung von Massenvorgängen im .NET SDK werden die [Task Parallel Library](/dotnet/standard/parallel-programming/task-parallel-library-tpl) und parallel ausgeführte Gruppierungsvorgänge genutzt. 

Im SDK gibt es keine einzelne Methode, die Ihre Liste mit Dokumenten oder Vorgängen als Eingabeparameter übernimmt. Sie müssen also eine Aufgabe für jeden Vorgang erstellen, den Sie in einem Massenvorgang ausführen möchten, und dann können Sie einfach warten, bis alle Aufgaben ausgeführt wurden.

Angenommen, die anfängliche Eingabe ist beispielsweise eine Liste von Elementen mit folgendem Schema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Wenn Sie einen Massenimport ausführen möchten (ähnlich der Verwendung von „BulkExecutor.BulkImportAsync“), erfordert dies gleichzeitige Aufrufe von `CreateItemAsync`. Beispiel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Wenn Sie ein *Massenupdate* ausführen möchten (ähnlich der Verwendung von [BulkExecutor.BulkUpdateAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), erfordert dies gleichzeitige Aufrufe der `ReplaceItemAsync`-Methode nach dem Aktualisieren jedes Elementwerts. Beispiel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

Wenn Sie eine *Massenlöschung* ausführen möchten (ähnlich der Verwendung von [BulkExecutor.BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), erfordert dies gleichzeitige Aufrufe von `DeleteItemAsync` mit der `id` und dem Partitionsschlüssel für jede Element. Beispiel:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Ergebniszustand der Erfassungsaufgabe

In den vorherigen Codebeispielen haben wir eine Liste paralleler Aufgaben erstellt und für jede dieser Aufgaben die `CaptureOperationResponse`-Methode aufgerufen. Bei dieser Methode handelt es sich um eine Erweiterung, mit der Sie ein *ähnliches Antwortschema* wie bei BulkExecutor verwalten können, indem Sie Fehler erfassen und die [Verwendung von Anforderungseinheiten](request-units.md) verfolgen.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Dabei ist die `OperationResponse` deklariert als:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Paralleles Ausführen von Vorgängen

Um den Umfang der gesamten Aufgabenliste nachzuverfolgen, verwenden wir die folgende Hilfsklasse:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

Die `ExecuteAsync`-Methode wartet, bis alle Vorgänge abgeschlossen sind, und Sie können sie wie folgt verwenden:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Erfassen von Statistiken

Der vorherige Code wartet, bis alle Vorgänge abgeschlossen sind, und berechnet dann die erforderlichen Statistiken. Diese Statistiken ähneln der [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse) der Bulk Executor-Bibliothek.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

Die `BulkOperationResponse` enthält Folgendes:

1. Die Gesamtdauer der Verarbeitung der Vorgangsliste durch die Unterstützung von Massenvorgängen
1. Die Anzahl der erfolgreichen Vorgänge
1. Die Anzahl der verbrauchten Anforderungseinheiten
1. Wenn Fehler auftreten, wird eine Liste von Tupeln angezeigt, die die Ausnahme und das zugehörige Element für die Protokollierung und Identifizierung enthalten.

## <a name="retry-configuration"></a>Konfigurieren von Wiederholungen

Für die Bulk Executor-Bibliothek stand eine [Anleitung](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) zur Verfügung, in der das Festlegen von `MaxRetryWaitTimeInSeconds` und `MaxRetryAttemptsOnThrottledRequests` für [RetryOptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) auf `0` empfohlen wurde, um die Steuerung an die Bibliothek zu delegieren.

Bei der Unterstützung von Massenvorgängen im .NET SDK gibt es kein verborgenes Verhalten. Sie können die Wiederholungsoptionen direkt über [CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) und [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests) konfigurieren.

> [!NOTE]
> Falls die bereitgestellten Anforderungseinheiten erheblich geringer als aufgrund der Menge an Daten erwartet ausfallen, sollten Sie höhere Werte festlegen. Der Massenvorgang dauert dann zwar länger, aber die Wahrscheinlichkeit, dass er vollständig erfolgreich ausgeführt wird, ist aufgrund der höheren Wiederholungsrate höher.

## <a name="performance-improvements"></a>Leistungsverbesserungen

Wie bei anderen Vorgängen mit dem .NET SDK führt die Verwendung der Stream-APIs zu einer besseren Leistung und einer Vermeidung unnötiger Serialisierungen. 

Die Verwendung von Stream-APIs ist nur möglich, wenn die verwendeten Datentypen, mit denen eines Bytestreams (z. B. Dateidatenströme) übereinstimmen. In solchen Fällen erhöht sich der Durchsatz, der erreicht werden kann, wenn Sie die Methoden `CreateItemStreamAsync`, `ReplaceItemStreamAsync` oder `DeleteItemStreamAsync` und die `ResponseMessage` (anstelle der `ItemResponse`) verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den .NET SDK-Releases finden Sie im Artikel [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md).
* Den vollständigen [Quellcode zur Migration](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) finden Sie auf GitHub.
* [Weitere Beispiele für Massenvorgänge auf GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)