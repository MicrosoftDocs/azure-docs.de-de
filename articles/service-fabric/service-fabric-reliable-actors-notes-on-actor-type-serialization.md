---
title: Hinweise zur Serialisierung des Actor-Typs in Reliable Actors | Microsoft Docs
description: Erörtert grundlegende Anforderungen für das Definieren serialisierbarer Klassen, die zum Definieren von Statusangaben und Schnittstellen für Service Fabric Reliable Actors verwendet werden können.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c8eeeb0ade6ca002adf3211cbf49127be9b76edb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725651"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Hinweise zur Typserialisierung von Service Fabric Reliable Actors
Die Argumente aller Methoden, die Ergebnistypen der von jeder Methode zurückgegebenen Aufgaben in einer Actor-Schnittstelle sowie Objekte, die im Zustands-Manager des Akteurs gespeichert sind, müssen [mit DataContract serialisiert werden können](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Dies gilt auch für die Argumente der Methoden, die in [Actor-Ereignisschnittstellen](service-fabric-reliable-actors-events.md)definiert werden. (In Actor-Ereignisschnittstellen definierte Methoden geben immer „void“ zurück).

## <a name="custom-data-types"></a>Benutzerdefinierte Datentypen
In diesem Beispiel definiert die folgende Actor-Schnittstelle eine Methode, die einen benutzerdefinierten Datentyp namens `VoicemailBox` zurückgibt.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Die Schnittstelle wird durch einen Akteur implementiert, der den Zustands-Manager zum Speichern eines `VoicemailBox`-Objekts verwendet:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

In diesem Beispiel wird das `VoicemailBox` -Objekt serialisiert, wenn:

* Das Objekt zwischen einer Actor-Instanz und einem Anrufer übertragen wird.
* Das Objekt im Zustands-Manager gespeichert wird, von wo aus es dauerhaft auf einem Datenträger gespeichert und auf die anderen Knoten repliziert wird.

Das Reliable Actor-Framework verwendet DataContract-Serialisierung. Aus diesem Grund müssen die benutzerdefinierten Objekte und deren Member mit den Anmerkungen **DataContract** beziehungsweise **DataMember** versehen sein.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Nächste Schritte
* [Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Actor-Timer und -Erinnerungen](service-fabric-reliable-actors-timers-reminders.md)
* [Actor-Ereignisse](service-fabric-reliable-actors-events.md)
* [Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)
* [Actor-Polymorphie und objektorientierte Entwurfsmuster](service-fabric-reliable-actors-polymorphism.md)
* [Actor-Diagnose und -Leistungsüberwachung](service-fabric-reliable-actors-diagnostics.md)
