---
title: Polymorphie im Reliable Actors-Framework | Microsoft Docs
description: Erstellen Sie Hierarchien von .NET-Schnittstellen und -Typen im Reliable Actors-Framework zur Wiederverwendung von Funktionen und API-Definitionen.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725488"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorphie im Reliable Actors-Framework
Das Reliable Actors-Framework ermöglicht es Ihnen, Actors mit vielen der Vorgehensweisen zu erstellen, die Sie auch beim objektorientierten Entwerfen verwenden würden. Eine dieser Techniken ist Polymorphie, bei der Typen und Schnittstellen von allgemeineren übergeordneten Elementen erben. Die Vererbung im Reliable Actors-Framework folgt normalerweise dem .NET-Modell mit einigen zusätzlichen Einschränkungen. Im Fall von Java/Linux folgt sie dem Java-Modell.

## <a name="interfaces"></a>Schnittstellen
Das Reliable Actors-Framework erfordert die Definition mindestens einer Schnittstelle, die von Ihrem Akteurtyp implementiert wird. Diese Schnittstelle wird verwendet, um eine Proxyklasse zu generieren, die von den Clients zur Kommunikation mit dem Akteur verwendet werden kann. Schnittstellen können von anderen Schnittstellen erben, solange jede durch einen Akteurtyp sowie alle übergeordneten Schnittstellen implementierte Schnittstelle letztendlich von IActor(C#) oder Actor(Java) abgeleitet wird. IActor(C#) und Actor(Java) sind die von der Plattform definierten Schnittstellen für Akteure in den Frameworks .NET und Java. Folglich kann das klassische Polymorphie-Beispiel mithilfe von Formen wie folgt aussehen:

![Schnittstellenhierarchie für Formakteure][shapes-interface-hierarchy]

## <a name="types"></a>Typen
Sie können auch eine Hierarchie von Akteurtypen erstellen, die von der durch die Plattform bereitgestellten Akteurbasisklasse abgeleitet werden. Bei Formen verfügen Sie möglicherweise über einen `Shape`(C#)- oder `ShapeImpl`(Java)-Basistyp:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Untertypen von `Shape`(C#) oder `ShapeImpl`(Java) können Methoden von der Basis außer Kraft setzen.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Beachten Sie das `ActorService` -Attribut des Akteurtyps. Dieses Attribut teilt dem Reliable Actor-Framework mit, dass es automatisch einen Dienst zum Hosten von Actors dieses Typs erstellen soll. In einigen Fällen sollten Sie möglicherweise einen Basistyp erstellen, der ausschließlich für die Freigabe von Funktionen für Untertypen vorgesehen ist und nicht verwendet wird, um tatsächliche Akteure zu instanziieren. In diesen Fällen sollten Sie das Schlüsselwort `abstract` verwenden, um anzugeben, dass Sie nie einen Akteur basierend auf diesem Typ erstellen werden.

## <a name="next-steps"></a>Nächste Schritte
* Hier finden Sie weitere Informationen darüber, [wie das Reliable Actors-Framework die Service Fabric-Plattform nutzt](service-fabric-reliable-actors-platform.md) , um Zuverlässigkeit, Skalierbarkeit und einen konsistenten Zustand zu gewährleisten.
* Erfahren Sie mehr über den [Actor-Lebenszyklus](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
