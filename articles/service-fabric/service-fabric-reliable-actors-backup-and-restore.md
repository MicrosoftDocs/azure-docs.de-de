---
title: Sichern und Wiederherstellen von Azure Service Fabric Actors
description: Hier erfahren Sie, wie Sie die Sicherung und Wiederherstellung in Azure Service Fabric Actors implementieren.
ms.topic: conceptual
ms.date: 10/29/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 05dfb7d09e68373f52831a9e5233316df95ee552
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571333"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Implementieren der Sicherung und Wiederherstellung von Reliable Actors

> [!NOTE]
> Microsoft empfiehlt die Verwendung von [Regelmäßigen Sicherungen und Wiederherstellungen](service-fabric-backuprestoreservice-quickstart-azurecluster.md) für die Konfiguration der Datensicherung von Reliable Stateful Services und Reliable Actors. 
> 

Im folgenden Beispiel stellt der benutzerdefinierte Actordienst eine Methode zum Sichern von Actordaten bereit. Diese nutzt die Vorteile des Remoting-Listeners, der bereits in `ActorService` enthalten ist:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

In diesem Beispiel ist `IMyActorService` ein Remotingvertrag, der `IService` (C#) und `Service` (Java) implementiert und dann durch `MyActorService` implementiert wird. Durch das Hinzufügen dieses Remotingvertrags stehen die Methoden in `IMyActorService` jetzt auch auf einem Client bereit, da ein Remotingproxy mit `ActorServiceProxy` erstellt wird:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

Weitere Informationen zu Reliable Actors finden Sie in den folgenden Artikeln:
* [Actor-Zustandsverwaltung](service-fabric-reliable-actors-state-management.md)
* [Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Actors API reference documentation](/previous-versions/azure/dn971626(v=azure.100))
* [.NET-Beispielcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-Beispielcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
