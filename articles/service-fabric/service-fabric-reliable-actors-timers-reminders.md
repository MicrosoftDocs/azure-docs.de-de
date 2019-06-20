---
title: Timer und Erinnerungen für Reliable Actors | Microsoft Docs
description: Einführung in Timer und Erinnerungen für Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 323de842645cced3c6f490e98112fcbcd184aa64
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726807"
---
# <a name="actor-timers-and-reminders"></a>Actor-Timer und -Erinnerungen
Akteure können regelmäßige Arbeit an sich selbst einplanen, indem sie entweder Timer oder Erinnerungen registrieren. In diesem Artikel wird gezeigt, wie Timer und Erinnerungen verwendet werden können, und es werden die Unterschiede zwischen ihnen erklärt.

## <a name="actor-timers"></a>Actor-Timer
Actor-Timer bieten einen einfachen Wrapper um .NET- oder Java-Timer, um sicherzustellen, dass die Rückrufmethoden die von der Actors-Runtime bereitgestellten wechselseitigen Parallelitätsgarantien respektieren.

Actors können sich mithilfe der Methoden `RegisterTimer`(C#) oder `registerTimer`(Java) und `UnregisterTimer`(C#) oder `unregisterTimer`(Java) ihrer Basisklasse bei den zugehörigen Timern an- und abmelden. Das folgende Beispiel zeigt die Verwendung von Timer-APIs. Die APIs sind dem .NET-Timer oder Java-Timer sehr ähnlich. In diesem Beispiel wird die `MoveObject`(C#)- oder `moveObject`(Java)-Methode von der Actors-Runtime aufgerufen, wenn der Timer abgelaufen ist. Es ist sichergestellt, dass die Methode die wechselseitige Parallelität respektiert. Dies bedeutet, dass keine anderen Actor-Methoden oder Timer-/Erinnerungsrückrufe ausgeführt werden, bis dieser Rückruf die Ausführung abgeschlossen hat.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Der nächste Timerzeitraum beginnt nach Abschluss des Rückrufs. Dies bedeutet, dass der Timer während des Rückrufs angehalten und nach Abschluss des Rückrufs erneut gestartet wird.

Die Actors-Laufzeit speichert die Änderungen, die im Zustands-Manager des Actors vorgenommen wurden, wenn der Rückruf endet. Tritt ein Fehler beim Speichern des Zustands auf, wird das Actor-Objekt deaktiviert und eine neue Instanz aktiviert.

Alle Timer werden angehalten, wenn der Actor im Rahmen einer Garbage Collection deaktiviert wird. Danach werden keine Timerrückrufe mehr aufgerufen. Zur Actor-Laufzeit werden zudem keine Informationen über die Timer gespeichert, die vor der Deaktivierung ausgeführt wurden. Es ist die Aufgabe des Actors, alle für zukünftige Reaktivierungen erforderlichen Timer zu registrieren. Weitere Informationen finden Sie im Abschnitt zur [Actor Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

## <a name="actor-reminders"></a>Actor-Erinnerungen
Erinnerungen sind ein Mechanismus zum Auslösen persistenter Rückrufe für einen Actor zu bestimmten Zeiten. Die Funktionalität ähnelt dabei Timern. Im Gegensatz zu Timern werden Erinnerungen aber unter allen Umständen ausgelöst, bis ihre Registrierung vom Actor explizit aufgehoben oder der Actor explizit gelöscht wird. Erinnerungen werden insbesondere über Actor-Deaktivierungen und Failover ausgelöst, da zur Actor-Laufzeit Informationen über die Erinnerungen des Actors mithilfe des Actor-Zustandsanbieters gespeichert werden. Beachten Sie, dass die Zuverlässigkeit von Erinnerungen an die Garantien für die Zuverlässigkeit des Zustands des Actor-Zustandsanbieters gebunden ist. Dies bedeutet, dass für Actors, deren Zustandsdauerhaftigkeit auf „Keine“ festgelegt wird, die Erinnerungen nach einem Failover nicht ausgelöst werden. 

Zum Registrieren einer Erinnerung ruft ein Actor die in der Basisklasse bereitgestellte `RegisterReminderAsync` -Methode auf, wie im folgenden Beispiel gezeigt:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

In diesem Beispiel ist `"Pay cell phone bill"` der Name der Erinnerung. Dies ist eine Zeichenfolge, die der Actor zur eindeutigen Identifizierung einer Erinnerung verwendet. `BitConverter.GetBytes(amountInDollars)`(C#) ist der mit der Erinnerung verknüpfte Kontext. Sie wird an den Actor als Argument für den Erinnerungsrückruf zurückgegeben, d.h. `IRemindable.ReceiveReminderAsync`(C#) oder `Remindable.receiveReminderAsync`(Java).

Actors, die Erinnerungen verwenden, müssen die `IRemindable` -Schnittstelle implementieren, wie im folgenden Beispiel gezeigt.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Wenn eine Erinnerung ausgelöst wird, ruft die Reliable Actors-Runtime die `ReceiveReminderAsync`(C#)- oder `receiveReminderAsync`(Java)-Methode für den Actor auf. Ein Actor kann mehrere Erinnerungen registrieren. Die `ReceiveReminderAsync`(C#)- oder `receiveReminderAsync`(Java)-Methode wird aufgerufen, sobald eine dieser Erinnerungen ausgelöst wurde. Der Actor kann mithilfe des an die `ReceiveReminderAsync`(C#)- oder `receiveReminderAsync`(Java)-Methode übergebenen Erinnerungsnamens ermitteln, welche Erinnerung ausgelöst wurde.

Die Actors-Runtime speichert den Actor-Zustand nach Abschluss des Aufrufs von `ReceiveReminderAsync`(C#) oder `receiveReminderAsync`(Java). Tritt ein Fehler beim Speichern des Zustands auf, wird das Actor-Objekt deaktiviert und eine neue Instanz aktiviert.

Zum Aufheben der Registrierung einer Erinnerung ruft ein Actor die `UnregisterReminderAsync`(C#)- oder `unregisterReminderAsync`(Java)-Methode auf, wie im folgenden Beispiel gezeigt.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Wie oben gezeigt, akzeptiert die `UnregisterReminderAsync`(C#)- oder `unregisterReminderAsync`(Java)-Methode eine `IActorReminder`(C#)- oder `ActorReminder`(Java)-Schnittstelle. Die Basisklasse des Actors unterstützt die `GetReminder`(C#)- oder `getReminder`(Java)-Methode, die zum Abrufen der `IActorReminder`(C#)- oder `ActorReminder`(Java)-Schnittstelle durch Übergabe des Erinnerungsnamens verwendet werden kann. Dies ist praktisch, da der Actor die `IActorReminder`(C#)- oder `ActorReminder`(Java)-Schnittstelle nicht beibehalten muss, die bei Aufruf der `RegisterReminder`(C#)- oder `registerReminder`(Java)-Methode zurückgegeben wurde.

## <a name="next-steps"></a>Nächste Schritte
Sie erhalten Informationen zu Reliable Actor-Ereignissen und Eintrittsinvarianz:
* [Actor-Ereignisse](service-fabric-reliable-actors-events.md)
* [Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)
