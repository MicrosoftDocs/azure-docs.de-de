---
title: Ereignisse in akteurbasierten Azure Service Fabric-Akteuren
description: Erfahren Sie mehr über Ereignisse für Service Fabric Reliable Actors, eine effektive Möglichkeit für die Kommunikation zwischen Akteur und Client.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 591e3539715ba0873e44f56a93d05df6552bb1ef
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571367"
---
# <a name="actor-events"></a>Actor-Ereignisse
Actor-Ereignisse bieten eine Möglichkeit zum Senden von Best-Effort-Nachrichten vom Actor an die Clients. Actor-Ereignisse dienen der Actor-Client-Kommunikation und sollten nicht für die Kommunikation zwischen Actors verwendet werden.

Die folgenden Codeausschnitte zeigen, wie Sie Actor-Ereignisse in Ihrer Anwendung verwenden können.

Definieren Sie eine Schnittstelle, die die vom Actor veröffentlichten Ereignisse beschreibt. Diese Schnittstelle muss von der `IActorEvents` -Schnittstelle abgeleitet werden. Die Argumente der Methoden müssen [Datenvertrag-serialisierbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)sein. Die Methoden müssen „void“ zurückgeben, da Ereignisbenachrichtigungen unidirektional und vom Typ „best effort“ sind.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Deklarieren Sie die vom Actor in der Actor-Schnittstelle veröffentlichten Ereignisse.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Implementieren Sie auf der Clientseite den Ereignishandler.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Erstellen Sie auf dem Client einen Proxy für den Actor, der das Ereignis veröffentlicht, und abonnieren Sie diese Ereignisse.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Im Failover-Fall kann ein Failover des Actors zu einem anderen Prozess oder Knoten erfolgen. Der Actor-Proxy verwaltet die aktiven Abonnements und verlängert automatisch deren Abonnement. Sie können das Intervall für die Abonnementverlängerung über die API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` steuern. Zum Kündigen des Abonnements verwenden Sie die API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` .

Veröffentlichen Sie auf dem Actor die Ereignisse in der Reihenfolge ihres Auftretens. Wenn für das Ereignis Abonnenten vorhanden sind, sendet die Actors-Laufzeit ihnen die Benachrichtigung.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Nächste Schritte
* [Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)
* [Actor-Diagnose und -Leistungsüberwachung](service-fabric-reliable-actors-diagnostics.md)
* [Actor-API-Referenzdokumentation](/previous-versions/azure/dn971626(v=azure.100))
* [C#-Beispielcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C#-Beispielcode für .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-Beispielcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)
