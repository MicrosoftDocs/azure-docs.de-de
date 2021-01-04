---
title: Erstellen eines akteurbasierten Diensts in Azure Service Fabric
description: Erfahren Sie, wie Sie Ihren ersten akteurbasierten Dienst in C# mithilfe von Service Fabric Reliable Actors erstellen, debuggen und bereitstellen.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 225ccb67153a33ed47af68ebb1549dce37426278
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573460"
---
# <a name="getting-started-with-reliable-actors"></a>Erste Schritte mit Reliable Actors
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-actors-get-started.md)
> * [Java unter Linux](./service-fabric-create-your-first-linux-application-with-java.md)

Dieser Artikel führt Sie durch das Erstellen und Debuggen einer einfachen Reliable Actor-Anwendung in Visual Studio. Weitere Informationen zu Reliable Actors finden Sie unter [Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, vergewissern Sie sich, dass sich das Setup der Entwicklungsumgebung, einschließlich Visual Studio, von Service Fabric auf Ihrem Computer befindet. Weitere Informationen finden Sie unter [Einrichten der Entwicklungsumgebung](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Erstellen eines neuen Projekts in Visual Studio

Starten Sie Visual Studio 2019 oder höher als Administrator, und erstellen Sie ein neues Projekt mit einer **Service Fabric-Anwendung**:

![Service Fabric-Tools für Visual Studio – Neues Projekt][1]

Wählen Sie im nächsten Dialogfeld unter **.NET Core 2.0** die Option **Actordienst** aus, und geben Sie einen Namen für den Dienst ein.

![Service Fabric-Projektvorlagen][5]

Das erstellte Projekt weist die folgende Struktur auf:

![Service Fabric-Projektstruktur][2]

## <a name="examine-the-solution"></a>Untersuchen der Lösung

Die Lösung enthält drei Projekte:

* **Das Anwendungsprojekt (MyApplication)** . Dieses Projekt fasst alle Dienste für die Bereitstellung zusammen. Es enthält die Datei *ApplicationManifest.xml* und PowerShell-Skripts zum Verwalten der Anwendung.

* **Das Schnittstellenprojekt (HelloWorld.Interfaces)** . Diese Projekt enthält die Schnittstellendefinition für den Actor. Actorschnittstellen können in jedem Projekt mit einem beliebigen Namen definiert werden.  Die Schnittstelle definiert den Actorvertrag. Dieser wird von der Actorimplementierung und den Clients, die den Actor aufrufen, gemeinsam genutzt.  Da Clientprojekte davon abhängig sein können, ist es in der Regel sinnvoll, ihn in einer Assembly zu definieren, die von der Actorimplementierung getrennt ist.

* **Das Actordienst-Projekt (HelloWorld)** . Dieses Projekt definiert den Service Fabric-Dienst zum Hosten des Actors. Es enthält die Implementierung des Akteurs, *HelloWorld.cs*. Eine Actorimplementierung ist eine Klasse, die vom Basistyp `Actor` abstammt und die im Projekt *MyActor.Interfaces* definierten Schnittstellen implementiert. Eine Actor-Klasse muss auch einen Konstruktor implementieren, der eine `ActorService`-Instanz und eine `ActorId` akzeptiert und diese an die `Actor`-Basisklasse übergibt.
    
    Dieses Projekt enthält außerdem die Datei *Program.cs*, mit der Actor-Klassen mit der Service Fabric-Laufzeit mithilfe von `ActorRuntime.RegisterActorAsync<T>()` registriert werden. Die `HelloWorld`-Klasse ist bereits registriert. Alle zusätzlichen Actorimplementierungen, die dem Projekt hinzugefügt werden, müssen auch in der `Main()`-Methode registriert werden.

## <a name="customize-the-helloworld-actor"></a>Anpassen des HelloWorld-Actors

Die Projektvorlage definiert einige Methoden in der `IHelloWorld`-Schnittstelle und implementiert sie in der `HelloWorld`-Actorimplementierung.  Ersetzen Sie diese Methoden, damit der Actordienst eine einfache „Hello World“-Zeichenfolge zurückgibt.

Ersetzen Sie im Projekt *HelloWorld.Interfaces* in der Datei *IHelloWorld.cs* die Schnittstellendefinition wie folgt:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Ersetzen Sie im Projekt **HelloWorld** in der Datei **HelloWorld.cs** die gesamte Klassendefinition wie folgt:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Drücken Sie **STRG+UMSCHALT+B**, um das Projekt zu erstellen. Stellen Sie sicher, dass alles kompiliert wird.

## <a name="add-a-client"></a>Hinzufügen eines Clients

Erstellen Sie eine einfache Konsolenanwendung zum Aufrufen des Actordiensts.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **Hinzufügen** > **Neues Projekt...** aus.

2. Wählen Sie unter den **.NET Core**-Projekttypen die Option **Konsolenanwendung (.NET Core)** aus.  Geben Sie dem Projekt den Namen *ActorClient*.
    
    ![Hinzufügen des Dialogfelds „Neues Projekt“][6]    
    
    > [!NOTE]
    > Eine Konsolenanwendung ist nicht die Art von Anwendung, die Sie normalerweise als Client in Service Fabric verwenden würden, aber sie ist ein praktisches Beispiel für das Debuggen und Testen mit dem lokalen Service Fabric-Cluster.

3. Die Konsolenanwendung muss eine 64-Bit-Anwendung sein, um die Kompatibilität mit dem Schnittstellenprojekt und anderen Abhängigkeiten aufrechtzuerhalten.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ActorClient**, und klicken Sie anschließend auf **Eigenschaften**.  Legen Sie auf der Registerkarte **Erstellen** für die Option **Zielplattform** den Wert **x64** fest.
    
    ![Buildeigenschaften][8]

4. Das Clientprojekt erfordert das zuverlässige NuGet-Paket „Actors“.  Klicken Sie auf **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.  Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Das NuGet-Paket und alle abhängigen Elemente werden im Projekt „ActorClient“ installiert.

5. Das Clientprojekt erfordert auch einen Verweis auf das Schnittstellenprojekt.  Klicken Sie im Projekt „ActorClient“ mit der rechten Maustaste auf **Abhängigkeiten**, und klicken Sie dann auf **Projektverweis hinzufügen...** .  Wählen Sie **Projekte > Projektmappe** aus (sofern nicht bereits ausgewählt), und aktivieren Sie dann das Kontrollkästchen neben **HelloWorld.Interfaces**.  Klicken Sie auf **OK**.
    
    ![Dialogfeld „Verweis hinzufügen“][7]

6. Ersetzen Sie im Projekt „ActorClient“ den gesamten Inhalt von *Program.cs* durch den folgenden Code:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Ausführen und Debuggen

Drücken Sie **F5**, um die Anwendung lokal im Service Fabric-Entwicklungscluster zu erstellen, bereitzustellen und auszuführen.  Während der Bereitstellung wird der Fortschritt im Fenster **Ausgabe** angezeigt.

![Service Fabric-Debugging – Ausgabefenster][3]

Wenn die Ausgabe den Text *Die Anwendung ist fertig* enthält, kann der Dienst mithilfe der Anwendung „ActorClient“ getestet werden.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ActorClient**, und klicken Sie anschließend auf **Debuggen** > **Neue Instanz starten**  Die Befehlszeilenanwendung sollte die Ausgabe des Actordiensts anzeigen.

![Anwendungsausgabe][9]

> [!TIP]
> Die Service Fabric Actors-Laufzeit gibt einige [Ereignisse und Leistungsindikatoren im Zusammenhang mit Actor-Methoden](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr darüber, [wie Reliable Actors die Service Fabric-Plattform verwenden](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png
