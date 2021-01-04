---
title: Erstellen mehrerer unabhängiger Azure Functions-Trigger für Cosmos DB
description: Hier erfahren Sie, wie Sie mehrere unabhängige Azure Functions-Trigger für Cosmos DB zum Erstellen ereignisgesteuerter Architekturen konfigurieren.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 78fff48a97965f0b80456cd3e56ed1507bc784fc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336672"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Erstellen mehrerer Azure Functions-Trigger für Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Artikel beschreibt, wie Sie mehrere Azure Functions-Trigger für Cosmos DB konfigurieren können, die parallel verwendet werden können und unabhängig voneinander auf Änderungen reagieren.

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Serverlose ereignisbasierte Funktionen, die mit dem Azure Functions-Trigger für Cosmos DB arbeiten und gemeinsam einen Leasecontainer nutzen" border="false":::

## <a name="event-based-architecture-requirements"></a>Anforderungen an die ereignisbasierte Architektur

Beim Erstellen serverloser Architekturen mit [Azure Functions](../azure-functions/functions-overview.md) gilt die [Empfehlung](../azure-functions/functions-best-practices.md#avoid-long-running-functions), kleine zusammenarbeitende Funktionsgruppen anstelle umfangreicher, zeitintensiver Funktionen zu erstellen.

Wenn Sie ereignisbasierte serverlose Flows mithilfe des [Azure Functions-Triggers für Cosmos DB](./change-feed-functions.md) erstellen, entsteht ein Szenario, in dem Sie immer dann mehrere Aktionen ausführen möchten, wenn in einem bestimmten [Azure Cosmos-Container](./account-databases-containers-items.md#azure-cosmos-containers) ein neues Ereignis auftritt. Wenn auszulösende Aktionen voneinander unabhängig sind, wäre die ideale Lösung, **pro auszuführende Aktion einen Azure Functions-Trigger für Cosmos DB zu erstellen** , wobei alle Trigger in demselben Azure Cosmos-Container auf Änderungen lauschen.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimieren von Containern für mehrere Trigger

Unter Berücksichtigung der *Anforderungen* des Azure Functions-Triggers für Cosmos DB benötigen wir einen zweiten Container zum Speichern des Status, auch als *Leasescontainer* bezeichnet. Bedeutet das nun, dass Sie für jede Azure-Funktion einen separaten Leasescontainer benötigen?

Hier haben Sie zwei Möglichkeiten:

* Sie erstellen **einen Leasescontainer pro Funktion** : Dieser Ansatz kann zusätzliche Kosten nach sich ziehen, es sei denn, Sie verwenden eine [Datenbank mit gemeinsam genutztem Durchsatz](./set-throughput.md#set-throughput-on-a-database). Beachten Sie, dass der minimale Durchsatz auf Containerebene 400 [Anforderungseinheiten](./request-units.md) beträgt, und bei den Leasescontainern wird er nur verwendet, um den Fortschritt zu prüfen und den Zustand beizubehalten.
* Sie verwenden einen **Leasescontainer und nutzen ihn gemeinsam** für alle Ihre Funktionen: Diese zweite Option nutzt die für den Container bereitgestellten Anforderungseinheiten besser, da sie mehreren Azure-Funktionen ermöglicht, denselben bereitgestellten Durchsatz gemeinsam zu verwenden.

Dieser Artikel soll Sie durch die Realisierung der zweiten Option führen.

## <a name="configuring-a-shared-leases-container"></a>Konfigurieren eines gemeinsam genutzten Leasescontainers

Um den gemeinsam genutzten Leasescontainer zu konfigurieren, müssen Sie Ihren Triggern nur das `LeaseCollectionPrefix`-[Attribut](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) hinzufügen, wenn Sie C# verwenden, oder das `leaseCollectionPrefix`-[Attribut](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) bei Verwendung von JavaScript. Der Wert des Attributs sollte ein logischer Deskriptor der Funktion des jeweiligen Triggers sein.

Angenommen, Sie haben drei Trigger: einen, der E-Mails sendet, einen, der eine Aggregation zum Erstellen einer materialisierten Sicht durchführt, und einen, der die Änderungen für eine spätere Analyse an einen anderen Speicher sendet. Sie könnten dann das `LeaseCollectionPrefix` „emails“ dem ersten zuweisen, „materialized“ dem zweiten und „analytics“ dem dritten.

Das Entscheidende ist, dass alle drei Trigger **dieselbe Leasescontainerkonfiguration verwenden können** (Konto, Datenbank und Containername).

Ein sehr einfaches Codebeispiel mit dem `LeaseCollectionPrefix`-Attribut in C# sieht wie folgt aus:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

Für JavaScript können Sie die Konfiguration mit dem `leaseCollectionPrefix`-Attribut auf die `function.json`-Datei anwenden:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Überwachen Sie immer die auf Ihrem gemeinsam genutzten Leasescontainer bereitgestellten Anforderungseinheiten. Da jeder Trigger, der ihn nutzt, die durchschnittliche Durchsatznutzung erhöht, müssen Sie möglicherweise den bereitgestellten Durchsatz erhöhen, wenn Sie die Anzahl der Azure-Funktionen erhöhen, die ihn verwenden.

## <a name="next-steps"></a>Nächste Schritte

* [Hier](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) finden Sie Informationen zur vollständigen Konfiguration des Azure Functions-Triggers für Cosmos DB.
* Ziehen Sie die erweiterte [Liste von Beispielen](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) für alle Sprachen zu Rate.
* Im [GitHub-Repository](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) finden Sie weitere Beispiele mit „serverlosen“ Anleitungen für Azure Cosmos DB und Azure Functions.
