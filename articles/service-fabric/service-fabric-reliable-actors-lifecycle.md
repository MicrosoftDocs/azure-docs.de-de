---
title: Übersicht über den Azure Service Fabric-Akteurlebenszyklus
description: Erläutert den Service Fabric Reliable Actor-Lebenszyklus, Garbage Collection und das manuelle Löschen von Actors und ihren Zuständen
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576042"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Actor-Lebenszyklus, automatische Garbage Collection und manuelles Löschen
Ein Actor wird aktiviert, wenn zum ersten Mal eine seiner Methoden aufgerufen wird. Ein Actor wird deaktiviert (Garbage Collection durch die Actors-Laufzeit), wenn er innerhalb eines konfigurierbaren Zeitraums nicht verwendet wird. Ein Actor und sein Zustand können jederzeit auch manuell gelöscht werden.

## <a name="actor-activation"></a>Actor-Aktivierung
Wenn ein Actor aktiviert wird, geschieht Folgendes:

* Wenn ein Aufruf für einen Actor eingeht und er nicht bereits aktiv ist, wird ein neuer Actor erstellt.
* Der Zustand des Actors wird geladen, wenn zuvor ein Zustand gespeichert wurde.
* Die `OnActivateAsync`-Methode (C#) oder die `onActivateAsync`-Methode (Java), die in der Implementierung des Akteurs außer Kraft gesetzt werden kann, wird aufgerufen.
* Der Actor gilt jetzt als aktiv.

## <a name="actor-deactivation"></a>Actor-Deaktivierung
Wenn ein Actor deaktiviert wird, geschieht Folgendes:

* Wenn ein Actor einige Zeit lang nicht verwendet wird, wird er aus der Tabelle der aktiven Actors entfernt.
* Die `OnDeactivateAsync`-Methode (C#) oder die `onDeactivateAsync`-Methode (Java), die in der Implementierung des Akteurs außer Kraft gesetzt werden kann, wird aufgerufen. Dadurch werden alle Timer für den Actor gelöscht. Actor-Vorgänge wie Statusänderungen dürfen von dieser Methode nicht aufgerufen werden.

> [!TIP]
> Die Fabric Actors-Laufzeit gibt einige [Ereignisse im Zusammenhang mit der Actor-Aktivierung und -Deaktivierung](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.
>
>

### <a name="actor-garbage-collection"></a>Actor-Garbage Collection
Wenn ein Akteur deaktiviert wird, werden Referenzen zum Akteurobjekt veröffentlicht, und es kann eine normale Garbage Collection durch den CLR (Common Language Runtime)- oder JVM (Java Virtual Machine)-Garbage Collector durchgeführt werden. Die Garbage Collection bereinigt nur das Actor-Objekt und entfernt **nicht** den Zustand im Zustands-Manager des Actors. Wenn der Actor das nächste Mal aktiviert ist, wird ein neues Actor-Objekt erstellt und sein Zustand wiederhergestellt.

Was zählt im Sinne der Deaktivierung und Garbage Collection als „wird verwendet“?

* Empfangen eines Aufrufs
* `IRemindable.ReceiveReminderAsync` (gilt nur, wenn der Actor Erinnerungen verwendet).

> [!NOTE]
> Falls der Actor Timer verwendet und der Timer-Rückruf aufgerufen wird, zählt er **nicht** als "wird verwendet".
>
>

Bevor das Thema „Deaktivierung“ ausführlicher behandelt wird, ist es wichtig, die folgenden Begriffe zu definieren:

* *Scanintervall*. Das Intervall, in dem die Actors-Laufzeit ihre Tabelle der aktiven Actors nach Actors durchsucht, die deaktiviert werden können und für die eine Garbage Collection durchgeführt werden kann. Der Standardwert dafür ist 1 Minute.
* *Leerlauftimeout*. Die Zeitspanne, in der ein Actor inaktiv (im Leerlauf) sein muss, ehe er deaktiviert und eine Garbage Collection ausgeführt werden kann. Der Standardwert hierfür sind 60 Minuten.

In der Regel müssen Sie diese Standardeinstellungen nicht ändern. Falls erforderlich, können diese Intervalle jedoch durch `ActorServiceSettings` geändert werden, wenn Sie Ihren [Actordienst](service-fabric-reliable-actors-platform.md)registrieren.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Für jeden aktiven Actor verfolgt die Actors-Laufzeit die Zeitspanne, während der er sich im Leerlauf befand (d.h. nicht verwendet wurde). Die Actors-Runtime überprüft jeden Akteur regelmäßig (`ScanIntervalInSeconds`), um festzustellen, ob eine Garbage Collection für ihn durchgeführt werden kann, und markiert ihn, wenn er sich seit einem gewissen Zeitraum (`IdleTimeoutInSeconds`) im Leerlauf befindet.

Bei jeder Verwendung eines Actors wird seine Leerlaufzeit auf 0 zurückgesetzt. Danach kann für den Actor nur dann eine Garbage Collection ausgeführt werden, wenn er erneut `IdleTimeoutInSeconds`lang im Leerlauf bleibt. Denken Sie daran, dass ein Actor als verwendet gilt, wenn entweder eine Actor-Schnittstellenmethode oder ein Actor-Erinnerungsrückruf ausgeführt wird. Ein Actor gilt **nicht** als verwendet, wenn der Timer-Rückruf ausgeführt wird.

Das folgende Diagramm zeigt den Lebenszyklus eines einzelnen Actors, um diese Konzepte zu verdeutlichen.

![Beispiel für Leerlaufzeit][1]

Das Beispiel zeigt die Auswirkung von Actor-Methodenaufrufen, Erinnerungen und Timern auf die Lebensdauer dieses Actors. Die folgenden Punkte im Hinblick auf das Beispiel sind besonders erwähnenswert:

* „ScanInterval“ und „IdleTimeout“ werden auf 5 und 10 festgelegt. (Einheiten sind hier nicht wichtig, da nur das Konzept veranschaulicht werden soll.)
* Der Scanvorgang für Actors, für die eine Garbage Collection ausgeführt werden soll, erfolgt in den Intervallen T = 0, 5, 10, 15, 20, 25, wie durch den Scanintervallwert 5 definiert.
* Ein periodischer Timer wird bei T = 4, 8, 12, 16, 20, 24 ausgelöst, und der Rückruf wird ausgeführt. Auf die Leerlaufzeit des Actors hat dies keine Auswirkungen.
* Ein Actor-Methodenaufruf bei T = 7 setzt die Leerlaufzeit auf 0 zurück und verzögert die Ausführung der Garbage Collection für den Actor.
* Ein Actor-Erinnerungs-Rückruf wird bei T = 14 ausgeführt und verzögert die Garbage Collection des Actors noch weiter.
* Während des Garbage Collection-Scanvorgangs bei T = 25 überschreitet die Leerlaufzeit des Actors schließlich den Leerlauftimeout-Wert von 10, und die Garbage Collection wird für den Actor ausgeführt.

Für einen Actor wird nie eine Garbage Collection durchgeführt, während er eine seiner Methoden ausführt, unabhängig davon, wie viel Zeit die Ausführung dieser Methode beansprucht. Wie bereits erwähnt, verhindert die Ausführung von Actor-Schnittstellenmethoden und Erinnerungs-Rückrufen die Durchführung der Garbage Collection, indem die Leerlaufzeit des Actors auf 0 zurückgesetzt wird. Durch die Ausführung von Timer-Rückrufen wird die Leerlaufzeit nicht auf 0 zurückgesetzt. Allerdings wird die Garbage Collection des Actors verzögert, bis der Timer-Rückruf abgeschlossen ist.

## <a name="manually-deleting-actors-and-their-state"></a>Manuelles Löschen von Actors und deren Zuständen
Eine Garbage Collection von deaktivierten Actors bereinigt nur das Actor-Objekt, entfernt jedoch keine Daten, die im Zustands-Manager eines Actors gespeichert sind. Wenn ein Actor wieder aktiviert wird, werden ihm seine Daten durch den Zustands-Manager wieder zur Verfügung gestellt. In Fällen, in denen Actors Daten im Zustands-Manager speichern, dann deaktiviert aber nie wieder aktiviert werden, kann die Bereinigung Ihrer Daten nötig sein.  Beispiele zum Löschen von Actors finden Sie unter [Löschen zuverlässiger Actors und ihrer Zustände](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Nächste Schritte
* [Actor-Timer und -Erinnerungen](service-fabric-reliable-actors-timers-reminders.md)
* [Actor-Ereignisse](service-fabric-reliable-actors-events.md)
* [Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)
* [Actor-Diagnose und -Leistungsüberwachung](service-fabric-reliable-actors-diagnostics.md)
* [Actor-API-Referenzdokumentation](/previous-versions/azure/dn971626(v=azure.100))
* [C#-Beispielcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-Beispielcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
