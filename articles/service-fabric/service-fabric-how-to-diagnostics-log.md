---
title: Generieren von Protokollereignissen aus einer .NET Service Fabric-App in Azure oder einem eigenständigen Cluster
description: Erfahren Sie, wie Sie der auf einem Azure-Cluster oder eigenständigen Cluster gehosteten .NET Service Fabric-Anwendung Protokollierung hinzufügen.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: d1b3dc25dd9bda9d7f9d9152c2a94cea8321f5cf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482606"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Hinzufügen von Protokollierung zur Service Fabric-Anwendung

Wenn Probleme auftreten, muss Ihre Anwendung genügend Informationen zum forensischen Debuggen bereitstellen. Die Protokollierung zählt zum Wichtigsten, mit dem Sie Ihre Service Fabric-Anwendung ergänzen können. Wenn Fehler auftreten, kann eine gute Protokollierung die beste Möglichkeit zu deren Untersuchung bieten. Die Analyse der Protokollmuster kann Ihnen Wege zur Verbesserung von Leistung oder Design Ihrer Anwendung aufweisen. Dieses Dokument veranschaulicht einige verschiedene Protokollierungsoptionen.

## <a name="eventflow"></a>EventFlow

Mit der [EventFlow-Bibliothekssuite](https://github.com/Azure/diagnostics-eventflow) können Anwendungen definieren, welche Diagnosedaten gesammelt und wo sie ausgegeben werden sollten. Von Leistungsindikatoren bis Anwendungsablaufverfolgungen – Diagnosedaten können sehr unterschiedlich sein. Sie werden in demselben Prozess wie die Anwendung ausgeführt, sodass der Kommunikationsaufwand minimal ist. Weitere Informationen zu EventFlow und Service Fabric finden Sie unter [Ereignisaggregation und -sammlung mithilfe von EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Verwenden strukturierter EventSource-Ereignisse

Die Definition von Nachrichtenereignissen nach Anwendungsfall ermöglicht Ihnen, Daten über ein Ereignis im Kontext des Ereignisses zusammenzufassen. Basierend auf den Namen oder Werten der angegebenen Ereigniseigenschaften können Sie einfacher suchen und filtern. Strukturieren vereinfacht das Lesen der Instrumentierungsausgabe, erfordert jedoch mehr Überlegungen und Zeit für das Definieren eines Ereignisses für jeden Anwendungsfall. 

Einige Ereignisdefinitionen können über die gesamte Anwendung hinweg gemeinsam genutzt werden. Beispielweise kann ein Start- oder Stoppereignis einer Methode für viele Dienste in einer Anwendung wiederverwendet werden. Ein domänenspezifischer Dienst, z.B. ein Auftragssystem, kann ein **CreateOrder**-Ereignis mit einem eigenen eindeutigen Ereignis aufweisen. Bei diesem Ansatz werden ggf. viele Ereignisse generiert, sodass unter Umständen die Koordinierung von Bezeichnern über Projektteams hinweg erforderlich wird. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Generisches Verwenden von EventSource

Da das Definieren bestimmter Ereignisse schwierig sein kann, werden häufig nur einige Ereignisse mit einem gemeinsamen Satz von Parametern definiert, die ihre Informationen in der Regel als Zeichenfolge ausgeben. Dadurch geht ein großer Teil der Strukturierung verloren, sodass es schwieriger ist, die Ergebnisse zu durchsuchen und zu filtern. Bei diesem Ansatz werden einige Ereignisse definiert, die in der Regel den Protokolliergraden entsprechen. Mit dem folgenden Codeausschnitt wird eine Debug- und Fehlermeldung definiert:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Auch die Nutzung einer Hybridlösung mit strukturierter und generischer Instrumentierung kann gut funktionieren. Die strukturierte Instrumentierung wird zum Melden von Fehlern und Metriken verwendet. Generische Ereignisse können für die ausführliche Protokollierung verwendet werden, die von Technikern für die Problembehandlung genutzt wird.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Die ASP.NET Core-Protokollierung ([Microsoft.Extensions.Logging NuGet-Paket](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) ist ein Protokollierungsframework, das eine Standardprotokollierungs-API für Ihre Anwendung bereitstellt. Unterstützung für andere Protokollierungs-Back-Ends kann per Plug-In in die ASP.NET Core-Protokollierung integriert werden. Dies bietet Ihnen vielfältige Möglichkeiten, die Protokollierung ohne umfangreiche Codeänderungen in Ihre Anwendung zu integrieren.

1. Fügen Sie das NuGet-Paket **Microsoft.Extensions.Logging** dem Projekt hinzu, das Sie instrumentieren möchten. Fügen Sie ggf. auch Anbieterpakete hinzu. Weitere Informationen finden Sie unter [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Protokollierung in ASP.NET Core).
2. Fügen Sie der Dienstdatei eine **using**-Direktive für **Microsoft.Extensions.Logging** hinzu.
3. Definieren Sie eine private Variable innerhalb Ihrer Dienstklasse.

   ```csharp
   private ILogger _logger = null;
   ```

4. Fügen Sie im Konstruktor der Dienstklasse diesen Code hinzu:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Beginnen Sie in den Methoden, den Code zu instrumentieren. Es folgen einige Beispiele:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Verwenden anderer Protokollierungsanbieter

Einige Drittanbieter verwenden den im obigen Abschnitt beschriebenen Ansatz, einschließlich [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/) und [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Sie können diese Elemente in die ASP.NET Core-Protokollierung einbetten oder separat verwenden. Serilog enthält ein Feature zur Erweiterung aller Nachrichten, die von einem Protokollierungstool gesendet werden. Dieses Feature kann nützlich sein, um den Dienstnamen, den Typ und die Partitionsinformationen auszugeben. Führen Sie diese Schritte aus, um diese Funktion in der ASP.NET Core-Infrastruktur zu verwenden:

1. Fügen Sie dem Projekt die NuGet-Pakete **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate** und **Serilog.Sinks.Observable** hinzu. 
2. Erstellen Sie eine `LoggerConfiguration` und die Protokollierungsinstanz.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Fügen Sie dem Dienstkonstruktor ein `Serilog.ILogger`-Argument hinzu, und übergeben Sie das neu erstellte Protokollierungstool.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. Im Dienstkonstruktor werden Eigenschaftserweiterungen für **ServiceTypeName**, **ServiceName**, **PartitionId** und **InstanceId** erstellt.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumentieren Sie den Code so wie bei der Verwendung von ASP.NET Core ohne SeriLog.

   >[!NOTE]
   >Wir empfehlen Ihnen, den statischen `Log.Logger` *nicht* für das vorherige Beispiel zu verwenden. Service Fabric kann mehrere Instanzen mit dem gleichen Diensttyp innerhalb eines Prozesses hosten. Wenn Sie den statischen `Log.Logger` nutzen, zeigt der letzte Writer der Eigenschaftserweiterungen Werte für alle ausgeführten Instanzen an. Dies ist ein Grund dafür, dass die _logger-Variable eine private Membervariable der Dienstklasse ist. Darüber hinaus müssen Sie den `_logger` für den allgemeinen Code verfügbar machen, der ggf. dienstübergreifend verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie weitere Informationen zu [Ereignis- und Protokollgenerierung auf Anwendungs- und Dienstebene](service-fabric-diagnostics-event-generation-app.md).
- Lesen Sie weitere Informationen über die Protokollierung mit [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) und [Microsoft Azure Diagnose](service-fabric-diagnostics-event-aggregation-wad.md).










