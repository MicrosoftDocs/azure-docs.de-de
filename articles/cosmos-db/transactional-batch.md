---
title: Transaktionale Batchvorgänge in Azure Cosmos DB mit dem .NET SDK
description: Hier erfahren Sie, wie Sie TransactionalBatch im Azure Cosmos DB .NET SDK zum Ausführen einer Gruppe von Punktvorgängen verwenden, die entweder erfolgreich oder fehlerhaft abgeschlossen werden.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 1f541b947c04619892291e47002ea9b0dbb6d38d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340560"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Transaktionale Batchvorgänge in Azure Cosmos DB mit dem .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ein transaktionaler Batch bezeichnet eine Gruppe von Punktvorgängen, die zusammen mit dem gleichen Partitionsschlüssel in einem Container entweder erfolgreich oder fehlerhaft ausgeführt werden müssen. Im .NET SDK wird die `TranscationalBatch`-Klasse verwendet, um diesen Batch von Vorgängen zu definieren. Wenn alle Vorgänge in der Reihenfolge, in der sie im transaktionalen Batchvorgang beschrieben sind, erfolgreich ausgeführt werden, wird für die Transaktion ein Commit ausgeführt. Wenn jedoch ein Vorgang fehlschlägt, wird für die gesamte Transaktion ein Rollback ausgeführt.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Was ist eine Transaktion in Azure Cosmos DB?

Eine Transaktion in einer typischen Datenbank kann als Folge von Vorgängen definiert werden, die als einzige logische Arbeitseinheit ausgeführt werden. Jede Transaktion bietet ACID-Eigenschaftsgarantien (Atomarität, Konsistenz, Isolation, Dauerhaftigkeit).

* Die **Atomarität** bzw. Unteilbarkeit gewährleistet, dass alle innerhalb einer Transaktion ausgeführten Vorgänge als eine einzige Einheit betrachtet werden und entweder alle Vorgänge committet werden oder keiner von ihnen.
* Die **Konsistenz** stellt sicher, dass die Daten zwischen den Transaktionen immer einen gültigen Zustand aufweisen.
* Die **Isolation** sorgt dafür, dass es niemals zu Konflikten zwischen zwei Transaktionen kommt. Viele kommerzielle Systeme bieten mehrere Isolationsstufen, die je nach Anforderungen der Anwendung genutzt werden können.
* Die **Dauerhaftigkeit** stellt sicher, dass jede Änderung, die in einer Datenbank committet wird, immer vorhanden ist.
Azure Cosmos DB unterstützt [vollständige ACID-konforme Transaktionen mit Momentaufnahmenisolation](database-transactions-optimistic-concurrency.md) für Vorgänge innerhalb desselben [logischen Partitionsschlüssels](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Transaktionale Batchvorgänge im Vergleich zu gespeicherten Prozeduren

Azure Cosmos DB unterstützt derzeit gespeicherte Prozeduren, die auch den Transaktionsbereich für Vorgänge bereitstellen. Transaktionale Batchvorgänge bieten jedoch die folgenden Vorteile:

* **Sprachoption** : Transaktionale Batches werden im SDK und in der Sprache unterstützt, mit der Sie bereits arbeiten, während gespeicherte Prozeduren in JavaScript geschrieben werden müssen.
* **Codeversionsverwaltung** : Die Versionsverwaltung von Anwendungscode und dessen Integration in die CI/CD-Pipeline ist wesentlich natürlicher als die Aktualisierung einer gespeicherten Prozedur zu orchestrieren und sicherzustellen, dass der Rollover zum richtigen Zeitpunkt erfolgt. Außerdem wird das Rollback von Änderungen vereinfacht.
* **Leistung** : Verringerte Latenz bei entsprechenden Vorgängen um bis zu 30 % im Vergleich zur Ausführung gespeicherter Prozeduren.
* **Serialisierung von Inhalten** : Jeder Vorgang in einem Transaktionsbatch kann benutzerdefinierte Serialisierungsoptionen für seine Nutzlast verwenden.

## <a name="how-to-create-a-transactional-batch-operation"></a>Erstellen eines transaktionalen Batchvorgangs

Beim Erstellen eines transaktionalen Batchvorgangs beginnen Sie mit einer Containerinstanz und rufen `CreateTransactionalBatch` auf:

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Als Nächstes müssen Sie `ExecuteAsync` aufrufen:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Nachdem die Antwort empfangen wurde, müssen Sie überprüfen, ob die Ausführung erfolgreich war oder nicht, und die Ergebnisse extrahieren:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Wenn ein Fehler auftritt, weist der fehlerhafte Vorgang einen Statuscode für den jeweiligen Fehler auf. Alle anderen Vorgänge weisen hingegen Statuscode 424 auf (fehlerhafte Abhängigkeit). Im folgenden Beispiel schlägt der Vorgang fehl, weil versucht wird, ein bereits vorhandenes Element zu erstellen (409 HttpStatusCode.Conflict). Durch Statuscodes können Sie die Ursache eines Transaktionsfehlers leichter erkennen.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Ausführung von transaktionalen Batchvorgängen

Wenn die `ExecuteAsync`-Methode aufgerufen wird, werden alle Vorgänge im `TransactionalBatch`-Objekt gruppiert, in eine einzige Nutzlast serialisiert und als einzelne Anforderung an den Azure Cosmos DB-Dienst gesendet.

Der Dienst empfängt die Anforderung, führt alle Vorgänge innerhalb eines Transaktionsbereichs aus und gibt eine Antwort mit dem gleichen Serialisierungsprotokoll zurück. Diese Antwort ist entweder eine erfolgreiche oder fehlerhafte Ausführung und enthält intern alle einzelnen Vorgangsantworten.

Das SDK stellt Ihnen die Antwort bereit, damit Sie das Ergebnis überprüfen und optional die einzelnen internen Vorgangsergebnisse extrahieren können.

## <a name="limitations"></a>Einschränkungen

Derzeit bestehen zwei bekannte Einschränkungen:

* Die Größenbeschränkung für Anforderungen in Azure Cosmos DB legt fest, dass die Größe der `TransactionalBatch`-Nutzlast 2 MB nicht überschreiten darf, und die maximale Ausführungszeit beträgt 5 Sekunden.
* Es besteht derzeit eine Begrenzung auf 100 Vorgänge pro `TransactionalBatch`, um sicherzustellen, dass die Leistung wie erwartet ist und innerhalb der SLAs liegt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr darüber, [welche Möglichkeiten TransactionalBatch bietet](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch).

* Sehen Sie sich die [Beispiele](sql-api-dotnet-v3sdk-samples.md) an, um weitere Verwendungsmöglichkeiten des Cosmos DB .NET SDK kennenzulernen.
