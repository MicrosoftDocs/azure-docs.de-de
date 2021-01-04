---
title: Verwalten des Azure Service Fabric-Zustands
description: Erfahren Sie, wie Sie auf den Zustand eines Reliable Actor von Azure Service Fabric zugreifen, diesen speichern oder entfernen, und machen Sie sich mit Überlegungen zum Entwerfen einer Anwendung vertraut.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571316"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Zugriff auf den Reliable Actors-Status, dessen Speichern und Entfernen
[Reliable Actors](service-fabric-reliable-actors-introduction.md) sind Singlethread-Objekte, die sich zum Kapseln von Logik und Zustand sowie zuverlässigen Beibehalten des Zustands eignen. Jede Actorinstanz verfügt über einen eigenen [Zustands-Manager](service-fabric-reliable-actors-state-management.md): Dabei handelt es sich um eine wörterbuchähnliche Datenstruktur, die zuverlässig Schlüssel-Wert-Paare speichert. Der Zustands-Manager ist ein Wrapper für einen Zustandsanbieter. Sie können ihn unabhängig von der verwendeten [Persistenzeinstellung](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) zum Speichern von Daten verwenden.

Schlüssel des Zustands-Managers müssen Zeichenfolgen sein. Werte sind generisch und können von beliebiger Art (u. a. benutzerdefiniert) sein. Im Zustands-Manager gespeicherte Werte müssen für den Datenvertrag serialisierbar sein, da sie abhängig von der Zustandspersistenzeinstellung eines Akteurs während der Replikation möglicherweise über das Netzwerk an andere Knoten übertragen und auf Datenträger geschrieben werden.

Der Zustands-Manager macht allgemeine Wörterbuchmethoden zum Verwalten des Zustands verfügbar. Diese ähneln den Methoden in Reliable Dictionary.

Informationen finden Sie unter [Bewährte Methoden](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Zugriffszustand
Der Zugriff auf den Zustand erfolgt nach Schlüssel über den Zustands-Manager. Alle Zustands-Manager-Methoden sind asynchron, da sie u.U. Datenträger-E/A erfordern, wenn Akteure über einen persistenten Zustand verfügen. Beim ersten Zugriff werden Zustandsobjekte im Arbeitsspeicher zwischengespeichert. Bei wiederholten Zugriffsvorgängen wird direkt aus dem Speicher auf Objekte zugegriffen, und die Rückgabe erfolgt synchron, ohne dass dabei Datenträger-E/A entstehen oder Aufwand für den asynchronen Kontextwechsel anfällt. In den folgenden Fällen wird ein Zustandsobjekt aus dem Cache entfernt:

* Eine Akteurmethode löst nach dem Abrufen eines Objekts aus dem Zustands-Manager eine nicht behandelte Ausnahme aus.
* Ein Akteur wird entweder nach seiner Deaktivierung oder nach einem Fehler wieder aktiviert.
* Der Zustandsanbieter lagert den Zustand an den Datenträger aus. Dieses Verhalten hängt von der Implementierung des Zustandsanbieters ab. Der Standardzustandsanbieter für die Einstellung `Persisted` weist dieses Verhalten auf.

Der Zustand kann mit einem *Get*-Standardvorgang abgerufen werden, der `KeyNotFoundException`(C#) oder `NoSuchElementException`(Java) auslöst, wenn kein Eintrag für den Schlüssel vorhanden ist:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Der Zustand kann auch mit einer *TryGet*-Methode abgerufen werden, die keine Ausnahme auslöst, wenn kein Eintrag für einen Schlüssel vorhanden ist:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Speichern des Zustands
Mit den Abrufmethoden des Zustands-Managers wird ein Verweis auf ein Objekt im lokalen Speicher zurückgegeben. Eine Änderung dieses Objekts im lokalen Speicher bewirkt nicht, dass es dauerhaft gespeichert wird. Wenn ein Objekt vom Zustands-Manager abgerufen und geändert wird, muss es wieder in den Zustands-Manager eingefügt werden, damit es dauerhaft gespeichert wird.

Der Zustand kann über eine nicht bedingte *Set`dictionary["key"] = value`-Methode eingefügt werden. Dies entspricht der Syntax* :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Sie können den Zustand mithilfe einer *Add*-Methode hinzufügen. Diese Methode löst `InvalidOperationException`(C#) oder `IllegalStateException`(Java) aus, wenn sie versucht, einen Schlüssel hinzuzufügen, die bereits vorhanden ist.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Sie können den Zustand auch mithilfe einer *TryAdd*-Methode hinzufügen. Diese Methode löst keine Ausnahme aus, wenn sie versucht, einen Schlüssel hinzuzufügen, die bereits vorhanden ist.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Am Ende einer Akteurmethode speichert der Zustands-Manager automatisch alle Werte, die in einem Einfüge- oder Aktualisierungsvorgang hinzugefügt oder geändert wurden. Je nach verwendeten Einstellungen kann ein Speichervorgang das persistente Speichern auf Datenträgern und die Replikation beinhalten. Werte, die nicht geändert wurden, werden nicht persistent gespeichert oder repliziert. Wenn keine Werte geändert wurden, geschieht beim Speichervorgang nichts. Tritt beim Speichern ein Fehler auf, wird der geänderte Zustand verworfen und der Ursprungszustand wieder geladen.

Der Zustand kann durch Aufrufen der `SaveStateAsync`-Methode für die Akteurbasis auch manuell gespeichert werden:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Entfernen des Zustands
Der Zustand kann durch Aufrufen der *Remove*-Methode dauerhaft aus dem Zustands-Manager eines Akteurs entfernt werden. Diese Methode löst `KeyNotFoundException`(C#) oder `NoSuchElementException`(Java) aus, wenn sie versucht, einen Schlüssel zu entfernen, die nicht vorhanden ist.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Sie können den Zustand mithilfe der *TryRemove*-Methode auch permanent entfernen. Diese Methode löst keine Ausnahme aus, wenn sie versucht, einen Schlüssel zu entfernen, der nicht vorhanden ist.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Zustand, der in Reliable Actors gespeichert wurde, muss serialisiert werden, bevor er in den Datenträger geschrieben und zur Hochverfügbarkeit repliziert wird. Erfahren Sie mehr über [Typserialisierung von Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Und erfahren Sie mehr über [Diagnose und Leistungsüberwachung für Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
