---
title: 'Vorgehensweise: Erstellen mehrerer unabhängiger Azure Cosmos DB-Trigger'
description: Erfahren Sie, wie Sie mehrere unabhängige Azure Cosmos DB-Trigger zum Erstellen ereignisgesteuerter Azure Functions-Architekturen konfigurieren.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: maquaran
ms.openlocfilehash: 722da9f0112d63af52be8c9c3a746f6da9638bac
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241951"
---
# <a name="create-multiple-azure-cosmos-db-triggers"></a>Erstellen mehrerer Azure Cosmos DB-Trigger

Dieser Artikel beschreibt, wie Sie mehrere Cosmos DB-Trigger zum parallelen Arbeiten und voneinander unabhängigen Reagieren auf Änderungen konfigurieren können.

![Mit dem Azure Cosmos DB-Trigger arbeitende und einen Container für Leases gemeinsam nutzende serverlose ereignisbasierte Funktionen](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Anforderungen an die ereignisbasierte Architektur

Beim Erstellen serverloser Architekturen mit [Azure Functions](../azure-functions/functions-overview.md) gilt die [Empfehlung](../azure-functions/functions-best-practices.md#avoid-long-running-functions), kleine zusammenarbeitende Funktionsgruppen anstelle umfangreicher, zeitintensiver Funktionen zu erstellen.

Wenn Sie ereignisbasierte serverlose Flows mithilfe des [Azure Cosmos DB-Triggers](./change-feed-functions.md) erstellen, entsteht ein Szenario, in dem Sie immer dann mehrere Dinge ausführen möchten, wenn in einem bestimmten [Azure Cosmos-Container](./databases-containers-items.md#azure-cosmos-containers) ein neues Ereignis auftritt. Wenn Aktionen, die Sie auslösen möchten, voneinander unabhängig sind, wäre die ideale Lösung, **pro Aktion, die Sie ausführen möchten, einen Cosmos DB-Trigger zu erstellen**, wobei alle Trigger auf demselben Azure Cosmos-Container auf Änderungen lauschen.

## <a name="optimizing-containers-for-multiple-triggers"></a>Optimieren von Containern für mehrere Trigger

Unter Berücksichtigung der *Anforderungen* des Cosmos DB-Triggers benötigen wir einen zweiten Container zum Speichern des Status, auch als *Leasescontainer* bezeichnet. Bedeutet das nun, dass Sie für jede Azure-Funktion einen separaten Leasescontainer benötigen?

Hier haben Sie zwei Möglichkeiten:

* Sie erstellen **einen Leasescontainer pro Funktion**: Dieser Ansatz kann zusätzliche Kosten nach sich ziehen, es sei denn, Sie verwenden eine [Datenbank mit gemeinsam genutztem Durchsatz](./set-throughput.md#set-throughput-on-a-database). Beachten Sie, dass der minimale Durchsatz auf Containerebene 400 [Anforderungseinheiten](./request-units.md) beträgt, und bei den Leasescontainern wird er nur verwendet, um den Fortschritt zu prüfen und den Zustand beizubehalten.
* Sie verwenden einen **Leasescontainer und nutzen ihn gemeinsam** für alle Ihre Funktionen: Diese zweite Option nutzt die für den Container bereitgestellten Anforderungseinheiten besser, da sie mehreren Azure-Funktionen ermöglicht, denselben bereitgestellten Durchsatz gemeinsam zu verwenden.

Dieser Artikel soll Sie durch die Realisierung der zweiten Option führen.

## <a name="configuring-a-shared-leases-container"></a>Konfigurieren eines gemeinsam genutzten Leasescontainers

Um den gemeinsam genutzten Leasescontainer zu konfigurieren, müssen Sie Ihren Triggern nur das `LeaseCollectionPrefix`-[Attribut](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) hinzufügen, wenn Sie C# verwenden, oder das `leaseCollectionPrefix`-[Attribut](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) bei Verwendung von JavaScript. Der Wert des Attributs sollte ein logischer Deskriptor der Funktion des jeweiligen Triggers sein.

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

* Informieren Sie sich über die vollständige Konfiguration des [Azure Cosmos DB-Triggers](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration).
* Ziehen Sie die erweiterte [Liste von Beispielen](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) für alle Sprachen zu Rate.
* Im [GitHub-Repository](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) finden Sie weitere Beispiele mit „serverlosen“ Anleitungen für Azure Cosmos DB und Azure Functions.