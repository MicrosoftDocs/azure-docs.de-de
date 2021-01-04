---
title: Implementieren von Features in Azure Service Fabric-Akteuren
description: Erfahren Sie, wie Sie einen eigenen Akteurdienst schreiben, der Funktionen auf Dienstebene genauso implementiert wie bei der Vererbung von StatefulService.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: d39ec93e0ad03d6c860bae9d0790e860c95457a5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575566"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementieren von Features auf Dienstebene in Ihrem Akteurdienst

Wie in [Dienstebenen](service-fabric-reliable-actors-platform.md#service-layering) beschrieben, ist der Actordienst selbst ein zuverlässiger Dienst. Sie können einen eigenen Dienst schreiben, der von `ActorService` abgeleitet ist. Sie können auch Features auf Dienstebene genauso implementieren wie beim Vererben eines zustandsbehafteten Diensts, z.B.:

- Dienstsicherung und -wiederherstellung
- Gemeinsame Verwendung von Funktionen für alle Akteure, z. B. ein Schaltkreisunterbrecher
- Remoteprozeduraufrufe für den Akteurdienst selbst sowie für einzelne Akteure

## <a name="use-the-actor-service"></a>Verwenden des Akteurdiensts

Akteurinstanzen haben Zugriff auf den Akteurdienst, in dem sie ausgeführt werden. Über den Akteurdienst können Akteurinstanzen den Dienstkontext programmgesteuert abrufen. Der Dienstkontext enthält die Partitions-ID, den Dienstnamen, den Anwendungsnamen und andere spezifische Informationen für die Azure Service Fabric-Plattform.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Wie alle Reliable Services muss der Akteurdienst mit einem Diensttyp in der Service Fabric-Laufzeit registriert werden. Damit der Akteurdienst Ihre Akteurinstanzen ausführen kann, muss auch Ihr Akteurtyp beim Akteurdienst registriert werden. Die `ActorRuntime` -Registrierungsmethode führt dies für Actors aus. Im einfachsten Fall müssen Sie nur den Akteurtyp registrieren, und der Akteurdienst verwendet dann die Standardeinstellungen.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Sie können auch ein Lambda verwenden, das von der Registrierungsmethode bereitgestellt wird, um selbst den Akteurdienst zu erstellen. Anschließend können Sie den Akteurdienst konfigurieren und Akteurinstanzen explizit erstellen. Sie können dann Abhängigkeiten über den Konstruktor in den Akteur einführen.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Akteurdienstmethoden

Der Akteurdienst implementiert `IActorService` (C#) oder `ActorService` (Java), mit denen wiederum `IService` (C#) bzw. `Service` (Java) implementiert wird. Diese Schnittstelle wird für das Reliable Services-Remoting verwendet, das Remoteprozeduraufrufe von Dienstmethoden erlaubt. Sie enthält Methoden auf Dienstebene, die mit dem Dienstremoting aufgerufen werden können. Verwenden Sie sie zum [Aufzählen](service-fabric-reliable-actors-enumerate.md) und [Löschen](service-fabric-reliable-actors-delete-actors.md) von Akteuren.


## <a name="custom-actor-service"></a>Benutzerdefinierter Akteurdienst

Mit dem Lambda für die Akteurregistrierung können Sie einen eigenen benutzerdefinierten Akteurdienst registrieren, der von `ActorService` (C#) und `FabricActorService` (Java) abgeleitet wird. Sie können dann eigene Funktionen auf Dienstebene implementieren, indem Sie eine Dienstklasse erstellen, die `ActorService` (C#) oder `FabricActorService` (Java) erbt. Ein benutzerdefinierter Akteurdienst erbt die gesamte Laufzeitfunktionalität des Akteurs von `ActorService` (C#) bzw. `FabricActorService` (Java). Er kann zum Implementieren eigener Dienstmethoden verwendet werden.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
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
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>Implementieren der Sicherung und Wiederherstellung von Akteuren

Der benutzerdefinierte Actordienst kann eine Methode zum Sichern von Actordaten bereitstellen. Diese nutzt die Vorteile des Remoting-Listeners, der bereits im `ActorService` enthalten ist. Ein Beispiel finden Sie unter [Implementieren von Sicherung und Wiederherstellung von Actors](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Akteur, der einen Remoting V2-Stapel (schnittstellenkompatibel) verwendet

Der Remoting V2-Stapel (schnittstellenkompatibel, auch als „V2_1“ bezeichnet) verfügt über alle Features des Remoting V2-Stapels. Die Schnittstelle ist mit dem Remoting V1-Stapel kompatibel, jedoch nicht abwärtskompatibel mit V2 und V1. Um ohne Auswirkungen auf die Dienstverfügbarkeit von V1 auf V2_1 zu aktualisieren, führen Sie die Schritte im nächsten Abschnitt aus.

Folgende Änderungen sind erforderlich, um den Remoting V2_1-Stapel verwenden zu können:

1. Fügen Sie Akteurschnittstellen das folgende assembly-Attribut hinzu.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Erstellen und aktualisieren Sie den Akteurdienst und Akteurclientprojekte, sodass in ihnen nun der V2-Stapel verwendet wird.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Upgrade für den Akteurddienst auf den Remoting V2-Stapel (schnittstellenkompatibel) ohne Beeinträchtigung der Dienstverfügbarkeit

Für diese Änderung wird ein Upgrade in zwei Schritten ausgeführt. Führen Sie die Schritte in dieser Reihenfolge aus.

1. Fügen Sie Akteurschnittstellen das folgende assembly-Attribut hinzu. Mit diesem Attribut werden zwei Listener für den Akteurdienst gestartet, V1 (vorhanden) und der V2_1-Listener. Aktualisieren Sie den Akteurdienst mit dieser Änderung.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Aktualisieren Sie die Akteurclients, nachdem Sie das vorherige Upgrade abgeschlossen haben.
   Mit diesem Schritt wird sichergestellt, dass der Akteurproxy den Remoting V2_1-Stapel verwendet.

3. Dieser Schritt ist optional. Ändern Sie das zuvor genannte Attribut, um den V1-Listener zu entfernen.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Akteur, der den Remoting V2-Stapel verwendet

Ab Version 2.8 des NuGet-Pakets können Benutzer den Remoting V2-Stapel verwenden, der leistungsfähiger ist und Features wie benutzerdefinierte Serialisierung bietet. Remoting V2 ist nicht abwärtskompatibel mit dem vorhandenen Remotingstapel (dieser wird jetzt als V1-Remotingstapel bezeichnet).

Die folgenden Änderungen sind erforderlich, um den Remoting V2-Stapel verwenden zu können.

1. Fügen Sie Akteurschnittstellen das folgende assembly-Attribut hinzu.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Erstellen und aktualisieren Sie den Akteurdienst und Akteurclientprojekte, sodass in ihnen nun der V2-Stapel verwendet wird.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Upgrade des Akteurdiensts auf den Remoting V2-Stapel, ohne dass die Dienstverfügbarkeit beeinträchtigt wird

Für diese Änderung wird ein Upgrade in zwei Schritten ausgeführt. Führen Sie die Schritte in dieser Reihenfolge aus.

1. Fügen Sie Akteurschnittstellen das folgende assembly-Attribut hinzu. Mit diesem Attribut werden zwei Listener für den Akteurdienst gestartet, V1 (vorhanden) und der V2-Listener. Aktualisieren Sie den Akteurdienst mit dieser Änderung.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Aktualisieren Sie die Akteurclients, nachdem Sie das vorherige Upgrade abgeschlossen haben.
   Mit diesem Schritt wird sichergestellt, dass der Akteurproxy den Remoting V2-Stapel verwendet.

3. Dieser Schritt ist optional. Ändern Sie das zuvor genannte Attribut, um den V1-Listener zu entfernen.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Nächste Schritte

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
