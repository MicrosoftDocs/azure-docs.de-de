---
title: Entwickeln von Komponententests für zustandsbehaftete Dienste in Azure Service Fabric | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Komponententests für zustandsbehaftete Service Fabric-Dienste entwickeln.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: b066296ca52d3067f8985245161eb4fa7b484a07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60720126"
---
# <a name="create-unit-tests-for-stateful-services"></a>Erstellen von Komponententests für zustandsbehaftete Dienste
Bei Unittests für zustandsbehaftete Service Fabric-Dienste werden häufige Fehler aufgedeckt, die durch herkömmliche anwendungs- oder domänenspezifische Unittests nicht unbedingt erkannt werden. Bei der Entwicklung von Komponententests für zustandsbehaftete Dienste sind einige besondere Aspekte zu berücksichtigen.

1. Jedes Replikat führt Anwendungscode aus, jedoch unter einem jeweils anderen Kontext. Wenn der Dienst drei Replikate verwendet, wird der Dienstcode parallel auf drei Knoten unter einem jeweils anderen Kontext (einer anderen Rolle) ausgeführt.
2. Der im zustandsbehafteten Dienst gespeicherte Zustand muss bei allen Replikaten konsistent sein. Der Zustands-Manager und zuverlässige Sammlungen stellen diese Konsistenz vorkonfiguriert bereit. Der In-Memory-Zustand muss jedoch vom Anwendungscode verwaltet werden.
3. Jedes Replikat ändert irgendwann die Rolle, während es im Cluster ausgeführt wird. Ein sekundäres Replikat wird ein primäres Replikat, falls der Knoten, auf dem das primäre Replikat gehostet wird, nicht mehr verfügbar oder überlastet ist. Dies ist ein natürliches Verhalten in Service Fabric, daher muss für Dienste geplant werden, dass sie irgendwann unter einer anderen Rolle ausgeführt werden.

In diesem Artikel wird vorausgesetzt, dass der Artikel [Ausführen von Unittests für zustandsbehaftete Dienste in Service Fabric](service-fabric-concepts-unit-testing.md) gelesen wurde.

## <a name="the-servicefabricmocks-library"></a>Die Bibliothek „ServiceFabric.Mocks“
Ab Version 3.3.0 enthält [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) eine API zum Simulieren der Orchestrierung der Replikate sowie der Zustandsverwaltung. Diese API wird in den Beispielen verwendet.

[NuGet](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks ist nicht Eigentum von Microsoft und wird nicht von Microsoft verwaltet. Diese Bibliothek wird jedoch derzeit von Microsoft für Unittests von zustandsbehafteten Diensten empfohlen.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Einrichten von Pseudoorchestrierung und Pseudozustand
Als Teil der Vorbereitungsphase eines Tests werden eine Pseudoreplikatgruppe und ein Pseudozustands-Manager erstellt. Die Replikatgruppe besitzt dann die Erstellung einer Instanz des getesteten Diensts für jedes Replikat. Sie besitzt auch die Ausführung von Lebenszyklusereignissen wie z.B. `OnChangeRole` und `RunAsync`. Der Pseudozustands-Manager stellt sicher, dass alle für den Zustands-Manager durchgeführten Vorgänge ausgeführt und beibehalten werden, wie dies auch der eigentliche Zustands-Manager sicherstellen würde.

1. Erstellen Sie einen Dienstfactorydelegat, der den getesteten Dienst instanziiert. Dieser sollte ähnlich oder identisch sein mit dem Dienstfactoryrückruf, der normalerweise für einen Service Fabric-Dienst oder Service Fabric-Actor in `Program.cs` gefunden wird. Dabei muss folgende Signatur beachtet werden:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Erstellen Sie eine Instanz der `MockReliableStateManager`-Klasse. Dadurch werden alle Interaktionen mit dem Zustands-Manager simuliert.
3. Erstellen Sie eine Instanz von `MockStatefulServiceReplicaSet<TStatefulService>`, bei der `TStatefulService` der Typ des zu testenden Diensts ist. Dazu werden der in Schritt 1 erstellte Delegat und der in Schritt 2 instanziierte Zustands-Manager benötigt.
4. Fügen Sie der Replikatgruppe Replikate hinzu. Geben Sie die Rolle (z.B. Primary, ActiveSecondary oder IdleSecondary) und die ID der Replikate an.
   > Notieren Sie sich die Replikat-IDs. Diese werden wahrscheinlich in der Aktions- und der Bestätigungsphase eines Komponententests verwendet.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Ausführen von Dienstanforderungen
Dienstanforderungen können mithilfe der praktischen Eigenschaften und Suchvorgänge für ein bestimmtes Replikat ausgeführt werden.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Ausführen einer Dienstverschiebung
Die Pseudoreplikatgruppe macht verschiedene praktische Methoden verfügbar, um unterschiedliche Arten von Dienstverschiebungen auszulösen.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Zusammenfügen des Gesamtbilds
Mit dem folgenden Test wird veranschaulicht, wie eine Replikatgruppe mit drei Knoten eingerichtet und dann überprüft wird, ob die Daten nach einer Rollenänderung über ein sekundäres Replikat verfügbar sind. Typischerweise wird hierbei möglicherweise aufgedeckt, ob die während `InsertAsync` hinzugefügten Daten ohne Ausführung von `CommitAsync` entweder In-Memory oder in einer zuverlässigen Sammlung gespeichert wurden. In beiden Fällen ist das sekundäre Replikat nicht mit dem primären Replikat synchronisiert. Dies führt nach Dienstverschiebungen zu inkonsistenten Antworten.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie die [Kommunikation zwischen Diensten testen](service-fabric-testability-scenarios-service-communication.md) und [Fehler mit kontrolliertem Chaos simulieren](service-fabric-controlled-chaos.md).
