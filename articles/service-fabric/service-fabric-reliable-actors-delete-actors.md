---
title: Löschen von Azure Service Fabric Actors | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Service Fabric Reliable Actors und deren Zustand manuell löschen.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726603"
---
# <a name="delete-reliable-actors-and-their-state"></a>Löschen von Reliable Actors und deren Zustand
Eine Garbage Collection von deaktivierten Actors bereinigt nur das Actor-Objekt, entfernt jedoch keine Daten, die im Zustands-Manager eines Actors gespeichert sind. Wenn ein Actor reaktiviert wird, werden seine Daten durch den Zustands-Manager wieder zur Verfügung gestellt. Wenn Actors Daten im Zustands-Manager speichern und dann deaktiviert, aber nicht wieder aktiviert werden, kann eine Bereinigung ihrer Daten nötig sein.

Der [Actordienst](service-fabric-reliable-actors-platform.md) bietet auch eine Funktion zum Löschen von Actors über einen Remoteanrufer:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Das Löschen eines Actors hat die folgenden Auswirkungen, abhängig davon, ob der Actor derzeit aktiv ist oder nicht:

* **Aktiver Actor**
  * Der Actor wird aus der Liste der aktiven Actors entfernt und deaktiviert.
  * Sein Zustand wird dauerhaft gelöscht.
* **Inaktiver Actor**
  * Sein Zustand wird dauerhaft gelöscht.

Ein Actor kann „delete“ nicht über eine seiner Actormethoden für sich selbst aufrufen, da er nicht gelöscht werden kann, während er in einem Actoraufrufkontext ausgeführt wird, in dem die Laufzeit eine Sperre für den Actoraufruf erwirkt hat, um einen Singlethread-Zugriff zu erzwingen.

Weitere Informationen zu Reliable Actors finden Sie hier:
* [Actor-Timer und -Erinnerungen](service-fabric-reliable-actors-timers-reminders.md)
* [Actor-Ereignisse](service-fabric-reliable-actors-events.md)
* [Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)
* [Actor-Diagnose und -Leistungsüberwachung](service-fabric-reliable-actors-diagnostics.md)
* [Actor-API-Referenzdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C#-Beispielcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-Beispielcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
