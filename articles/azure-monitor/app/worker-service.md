---
title: Application Insights für Workerdienst-Apps (Nicht-HTTP-Apps)
description: Überwachen von .NET Core- und .NET Framework-Apps ohne HTTP mit Azure Monitor Application Insights
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: c1ca594626d4384c9dfb62990ee2017d2094fca4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371854"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights für Workerdienstanwendungen (Anwendungen ohne HTTP)

Das [Application Insights SDK für Workerdienstanwendungen](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ist ein neues SDK, das sich perfekt für Workloads ohne HTTP eignet, z. B. Messaging, Hintergrundaufgaben, Konsolenanwendungen usw. Für diese Anwendungstypen gibt es keine eingehenden HTTP-Anforderungen wie bei einer herkömmlichen ASP.NET-/ASP.NET Core-Webanwendung. Deshalb wird die Verwendung von Application Insights-Paketen für [ASP.NET](asp-net.md)- und [ASP.NET Core](asp-net-core.md)-Anwendungen nicht unterstützt.

Das neue SDK sammelt keine Telemetriedaten. Stattdessen nutzt es andere bekannte automatische Application Insights-Collectors, z. B. [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) usw. Dieses SDK macht Erweiterungsmethoden in `IServiceCollection` verfügbar, um die Erfassung von Telemetriedaten zu aktivieren und zu konfigurieren.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Das [Application Insights SDK für Workerdienstanwendungen](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) eignet sich perfekt für Anwendungen ohne HTTP, unabhängig davon, wo und wie sie ausgeführt werden. Wenn Ihre Anwendung ausgeführt wird und über eine Netzwerkverbindung mit Azure verfügt, können Telemetriedaten erfasst werden. Die Application Insights-Überwachung wird in allen Umgebungen unterstützt, in denen auch .NET Core unterstützt wird. Dieses Paket kann in den neu eingeführten [.NET Core 3.0-Workerdienst](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [-Hintergrundaufgaben in ASP.NET Core 2.1/2.2](/aspnet/core/fundamentals/host/hosted-services), Konsolen-Apps (.NET Core/.NET Framework) usw. verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Ein gültiger Application Insights-Instrumentierungsschlüssel. Dieser ist erforderlich, um Telemetriedaten an Application Insights zu senden. Wenn Sie eine neue Application Insights-Ressource erstellen müssen, um einen Instrumentierungsschlüssel abzurufen, finden Sie unter [Erstellen einer Application Insights-Ressource](./create-new-resource.md) weitere Informationen.

## <a name="using-application-insights-sdk-for-worker-services"></a>Verwenden des Application Insights SDK für Workerdienstanwendungen

1. Installieren Sie das Paket [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) für die Anwendung.
   Der folgende Codeausschnitt zeigt die Änderungen, die Sie der `.csproj`-Datei Ihres Projekts hinzufügen müssen.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Rufen Sie die `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)`-Erweiterungsmethode für `IServiceCollection` auf, um den Instrumentierungsschlüssel bereitzustellen. Diese Methode sollte am Anfang der Anwendung aufgerufen werden. Der genaue Speicherort hängt vom Anwendungstyp ab.

1. Rufen Sie eine `ILogger`-Instanz oder `TelemetryClient`-Instanz aus dem Abhängigkeitsinjektionscontainer (Dependency Injection, DI) ab, indem Sie `serviceProvider.GetRequiredService<TelemetryClient>();` aufrufen oder die Konstruktorinjektion verwenden. Durch diesen Schritt wird das Einrichten des `TelemetryConfiguration`-Moduls und des Moduls für die automatische Sammlung ausgelöst.

Spezielle Anweisungen für die einzelnen Anwendungstypen finden Sie in den folgenden Abschnitten.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0-Workerdienstanwendung

Das vollständige Beispiel finden Sie [hier](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights).

1. Laden Sie [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) herunter, und installieren Sie es.
2. Erstellen Sie mit einer Visual Studio-Projektvorlage oder mit der Befehlszeile `dotnet new worker` ein neues Workerdienstprojekt.
3. Installieren Sie das Paket [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) für die Anwendung.

4. Fügen Sie wie im folgenden Beispiel dargestellt `services.AddApplicationInsightsTelemetryWorkerService();` der `CreateHostBuilder()`-Methode in Ihrer `Program.cs`-Klasse hinzu:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Ändern Sie `Worker.cs` gemäß dem folgenden Beispiel.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Richten Sie den Instrumentierungsschlüssel ein.

    Sie können einen Instrumentierungsschlüssel als Argument für `AddApplicationInsightsTelemetryWorkerService` bereitstellen. Empfohlen wird aber, den Instrumentierungsschlüssel in der Konfiguration anzugeben. Im folgenden Beispielcode wird veranschaulicht, wie Sie in `appsettings.json` einen Instrumentierungsschlüssel angeben. Stellen Sie sicher, dass `appsettings.json` während der Veröffentlichung in den Stammordner der Anwendung kopiert wird.

```json
    {
        "ApplicationInsights":
        {
            "InstrumentationKey": "putinstrumentationkeyhere"
        },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Alternativ können Sie den Instrumentierungsschlüssel auch in einer der folgenden Umgebungsvariablen angeben.
`APPINSIGHTS_INSTRUMENTATIONKEY` oder `ApplicationInsights:InstrumentationKey`

Beispiel: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
ODER `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

In der Regel wird mit `APPINSIGHTS_INSTRUMENTATIONKEY` der Instrumentierungsschlüssel für Anwendungen festgelegt, die in Web-Apps als Webaufträge bereitgestellt werden.

> [!NOTE]
> Ein Instrumentierungsschlüssel, der im Code angegeben ist, erhält Vorrang vor der Umgebungsvariable `APPINSIGHTS_INSTRUMENTATIONKEY`, die wiederum Vorrang vor anderen Optionen hat.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core-Hintergrundaufgaben mit gehosteten Diensten

In [diesem Dokument](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio) wird das Erstellen von Hintergrundaufgaben in einer ASP.NET Core 2.1/2.2-Anwendung beschrieben.

Das vollständige Beispiel finden Sie [hier](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService).

1. Installieren Sie das Paket [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) für die Anwendung.
2. Fügen Sie wie im folgenden Beispiel dargestellt `services.AddApplicationInsightsTelemetryWorkerService();` der `ConfigureServices()`-Methode hinzu:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Im folgenden Code für `TimedHostedService` befindet sich die Hintergrundaufgabenlogik.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Richten Sie den Instrumentierungsschlüssel ein.
   Verwenden Sie die Datei `appsettings.json` aus dem obigen Beispiel für den .NET Core 3.0-Workerdienst.

## <a name="net-corenet-framework-console-application"></a>.NET Core-/.NET Framework-Konsolenanwendung

Wie am Anfang dieses Artikels erwähnt, kann das neue Paket verwendet werden, um Application Insights-Telemetrie sogar aus einer regulären Konsolenanwendung zu aktivieren. Dieses Paket ist für [`NetStandard2.0`](/dotnet/standard/net-standard) vorgesehen und kann daher für Konsolenanwendungen in .NET Core 2.0 oder höher und .NET Framework 4.7.2 oder höher verwendet werden.

Das vollständige Beispiel finden Sie [hier](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights).

1. Installieren Sie das Paket [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) für die Anwendung.

2. Ändern Sie „Program.cs“ gemäß dem folgenden Beispiel.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Diese Konsolenanwendung verwendet dieselbe Standard-`TelemetryConfiguration` und kann auf die gleiche Weise wie die Beispiele im vorherigen Abschnitt angepasst werden.

## <a name="run-your-application"></a>Ausführen der Anwendung

Führen Sie Ihre Anwendung aus. Die Worker in den obigen Beispielen führen jede Sekunde einen HTTP-Aufruf von „bing.com“ aus und geben mithilfe von `ILogger` einige Protokolle aus. Diese Zeilen sind in einem `StartOperation`-Aufruf von `TelemetryClient` enthalten, der zum Erstellen eines Vorgangs verwendet wird (in diesem Beispiel `RequestTelemetry` mit dem Namen „operation“). Application Insights erfasst diese ILogger-Protokolle (standardmäßig mit dem Schweregrad „Warnung“ oder einem höheren Schweregrad) und Abhängigkeiten, und sie werden in einer Beziehung zwischen über- und untergeordneten Elementen mit `RequestTelemetry` korreliert. Die Korrelation kann auch prozessübergreifend und über Netzwerkgrenzen hinweg erfolgen. Wenn der Aufruf z. B. für eine andere überwachte Komponente erfolgt ist, wird sie ebenfalls mit dem übergeordneten Element korreliert.

Dieser benutzerdefinierte `RequestTelemetry`-Vorgang entspricht einer eingehenden Webanforderung in einer typischen Webanwendung. Obwohl es nicht erforderlich ist, einen Vorgang zu verwenden, eignet sich dies am besten für das [Korrelationsdatenmodell in Application Insights](./correlation.md). Dabei fungiert `RequestTelemetry` als übergeordneter Vorgang, und alle in der Workeriteration generierten Telemetriedaten werden als logisch zu demselben Vorgang gehörend behandelt. Mit diesem Ansatz wird auch sichergestellt, dass alle (automatisch und manuell) generierten Telemetriedaten dieselbe `operation_id` aufweisen. Wenn die Stichprobenentnahme auf der `operation_id` basiert, werden alle Telemetriedaten aus einer einzelnen Iteration entweder beibehalten oder gelöscht.

Nachfolgend werden alle automatisch von Application Insights erfassten Telemetriedaten aufgeführt.

### <a name="live-metrics"></a>Livemetriken

Mit [Livemetriken](./live-stream.md) kann schnell überprüft werden, ob die Application Insights-Überwachung ordnungsgemäß konfiguriert ist. Es kann einige Minuten dauern, bis die Telemetriedaten im Portal und in der Analyse angezeigt werden. In Livemetriken wird die CPU-Auslastung des laufenden Prozesses dagegen nahezu in Echtzeit angezeigt. Außerdem können andere Telemetriedaten wie z. B. Anforderungen, Abhängigkeiten oder Ablaufverfolgungen angezeigt werden.

### <a name="ilogger-logs"></a>ILogger-Protokolle

Protokolle, die über `ILogger` mit dem Schweregrad `Warning` oder höher ausgegeben werden, werden automatisch erfasst. Informationen zur Anpassung der in Application Insights erfassten Protokollebenen finden Sie in der [Dokumentation zu ILogger](ilogger.md#control-logging-level).

### <a name="dependencies"></a>Abhängigkeiten

Die Abhängigkeitssammlung ist standardmäßig aktiviert. In [diesem Artikel](asp-net-dependencies.md#automatically-tracked-dependencies) werden die Abhängigkeiten, die automatisch gesammelt werden, sowie die Schritte zur manuellen Nachverfolgung erläutert.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` ist standardmäßig aktiviert und sammelt einen Standardsatz von Leistungsindikatoren von .NET Core 3.0-Apps. Im Tutorial zu [EventCounter](eventcounters.md) ist der Standardsatz der gesammelten Leistungsindikatoren aufgeführt. Außerdem enthält es Anweisungen zum Anpassen der Liste.

### <a name="manually-tracking-additional-telemetry"></a>Manuelles Nachverfolgen zusätzlicher Telemetriedaten

Obwohl das SDK wie oben erläutert automatisch Telemetriedaten erfasst, muss der Benutzer in den meisten Fällen zusätzliche Telemetriedaten an Application Insights senden. Die empfohlene Methode zum Nachverfolgen zusätzlicher Telemetriedaten besteht darin, eine Instanz von `TelemetryClient` aus der Abhängigkeitsinjektion abzurufen und dann eine der unterstützten `TrackXXX()`-[API](api-custom-events-metrics.md)-Methoden in dieser aufzurufen. Ein weiterer typischer Anwendungsfall ist das [benutzerdefinierte Nachverfolgen von Vorgängen](custom-operations-tracking.md). Diese Vorgehensweise wird in den obigen Workerbeispielen veranschaulicht.

## <a name="configure-the-application-insights-sdk"></a>Konfigurieren des Application Insights SDK

Die vom Workerdienst-SDK verwendete Standard-`TelemetryConfiguration` ähnelt der automatischen Konfiguration in einer ASP.NET- oder ASP.NET Core-Anwendung, sie verfügt jedoch nicht über die TelemetryInitializers, die zum Anreichern von Telemetriedaten aus `HttpContext` verwendet werden.

Sie können das Application Insights SDK für Workerdienstanwendungen anpassen, um die Standardkonfiguration zu ändern. Benutzer des ASP.NET Core SDK für Application Insights wissen möglicherweise, wie sie die Konfiguration mit der in ASP.NET Core integrierten [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) anpassen. Das WorkerService SDK basiert auf ähnlichen Prinzipien. Führen Sie nahezu alle Änderungen im Abschnitt `ConfigureServices()` durch, indem Sie die entsprechenden Methoden für `IServiceCollection` aufrufen, wie unten beschrieben.

> [!NOTE]
> Wenn Sie dieses SDK verwenden, wird das Ändern der Konfiguration durch Ändern von `TelemetryConfiguration.Active` nicht unterstützt, und die Änderungen haben keine Auswirkungen.

### <a name="using-applicationinsightsserviceoptions"></a>Verwenden von ApplicationInsightsServiceOptions

Sie können wie im folgenden Beispiel einige allgemeine Einstellungen ändern, indem Sie der `AddApplicationInsightsTelemetryWorkerService`-Methode `ApplicationInsightsServiceOptions` übergeben:

```csharp
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    var aiOptions = new ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
}
```

Beachten Sie, dass sich `ApplicationInsightsServiceOptions` in diesem SDK im Namespace `Microsoft.ApplicationInsights.WorkerService` befindet, während es sich im ASP.NET Core SDK im Namespace `Microsoft.ApplicationInsights.AspNetCore.Extensions` befindet.

Häufig verwendete Einstellungen in `ApplicationInsightsServiceOptions`

|Einstellung | BESCHREIBUNG | Standard
|---------------|-------|-------
|EnableQuickPulseMetricStream | „LiveMetrics“-Feature aktivieren/deaktivieren | true
|EnableAdaptiveSampling | Adaptive Stichprobenerstellung aktivieren/deaktivieren | true
|EnableHeartbeat | Heartbeats-Feature aktivieren/deaktivieren, das in regelmäßigen Abständen (Standardwert: 15 Minuten) eine benutzerdefinierte Metrik namens „HeartBeatState“ mit Informationen zur Laufzeit wie .NET-Version, ggf. Informationen zur Azure-Umgebung usw. sendet. | true
|AddAutoCollectedMetricExtractor | Extraktor für „AutoCollectedMetrics“ aktivieren/deaktivieren, bei dem es sich um einen Telemetrieprozessor handelt, der vorab aggregierte Metriken zu Anforderungen/Abhängigkeiten sendet, bevor die Stichprobenerstellung stattfindet. | true
|EnableDiagnosticsTelemetryModule | `DiagnosticsTelemetryModule` aktivieren/deaktivieren. Wenn Sie diese Einstellung deaktivieren, werden die folgenden Einstellungen ignoriert: `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule`. | true

Die aktuelle Liste finden Sie unter den [konfigurierbaren Einstellungen in `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Stichproben

Das Application Insights SDK für Workerdienstanwendungen unterstützt sowohl die feste als auch die adaptive Stichprobenerstellung. Die adaptive Stichprobenerstellung ist standardmäßig aktiviert. Die Stichprobenentnahme kann über die Option `EnableAdaptiveSampling` in [ApplicationInsightsServiceOptions](#using-applicationinsightsserviceoptions) deaktiviert werden.

Das folgende Beispiel kann verwendet werden, um zusätzliche Einstellungen für die Stichprobenentnahme zu konfigurieren.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new ApplicationInsightsServiceOptions();
    
    // Disable adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);

    // Add Adaptive Sampling with custom settings.
    // the following adds adaptive sampling with 15 items per sec.
    services.Configure<TelemetryConfiguration>((telemetryConfig) =>
        {
            var builder = telemetryConfig.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond: 15);
            builder.Build();
        });
    //...
}
```

Weitere Informationen finden Sie im Dokument zur [Stichprobenentnahme](#sampling).

### <a name="adding-telemetryinitializers"></a>Hinzufügen von TelemetryInitializers

Mit [Telemetrieinitialisierern](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) legen Sie die Eigenschaften fest, die zusammen mit allen Telemetriedaten gesendet werden.

Fügen Sie jeden neuen `TelemetryInitializer` dem Container `DependencyInjection` hinzu, und das SDK fügt ihn automatisch der `TelemetryConfiguration` hinzu.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Entfernen von TelemetryInitializer-Elementen

Telemetrieinitialisierer sind standardmäßig vorhanden. Wenn Sie alle oder nur bestimmte Telemetrieinitialisierer entfernen möchten, können Sie den folgenden Beispielcode *nach* dem Aufrufen von `AddApplicationInsightsTelemetryWorkerService()` verwenden.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Hinzufügen von Telemetrieprozessoren

Sie können `TelemetryConfiguration` benutzerdefinierte Telemetrieprozessoren hinzufügen, indem Sie die Erweiterungsmethode `AddApplicationInsightsTelemetryProcessor` in `IServiceCollection` verwenden. Mit Telemetrieprozessoren können Sie in [komplexen Filterszenarios](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) genauer steuern, welche Informationen in die Telemetriedaten, die an Application Insights gesendet werden, einbezogen oder daraus ausgeschlossen werden. Verwenden Sie das folgende Beispiel.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurieren oder Entfernen von TelemetryModule-Standardelementen

Application Insights verwendet Telemetriemodule, um automatisch Telemetriedaten zu bestimmten Workloads zu erfassen, ohne dass eine manuelle Nachverfolgung erforderlich ist.

Die unten aufgeführten Module sind standardmäßig aktiviert. Diese sind verantwortlich für die automatische Erfassung von Telemetriedaten. Sie können sie deaktivieren oder ihr Standardverhalten anpassen.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` (Aktuell gibt es ein Problem mit diesem Telemetriemodul. Eine temporäre Problemumgehung finden Sie unter [GitHub-Problem 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
).)
* `AzureInstanceMetadataTelemetryModule`

Verwenden Sie zum Konfigurieren von `TelemetryModule`-Standardelementen die Erweiterungsmethode `ConfigureTelemetryModule<T>` in `IServiceCollection`. Dies ist im Beispiel unten dargestellt.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurieren des Telemetriekanals

Der Standardkanal ist `ServerTelemetryChannel`. Sie können diesen wie im folgenden Beispiel dargestellt überschreiben.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Dynamisches Deaktivieren von Telemetrie

Wenn Sie Telemetriedaten bedingt und dynamisch deaktivieren möchten, können Sie eine `TelemetryConfiguration`-Instanz mit einem ASP.NET Core-Dependency-Injection-Container an einer beliebigen Stelle im Code auflösen und ein `DisableTelemetry`-Flag dafür festlegen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Wie kann ich Telemetriedaten nachverfolgen, die nicht automatisch erfasst werden?

Rufen Sie eine Instanz von `TelemetryClient` ab, indem Sie die Abhängigkeit über den Konstruktor übergeben (Constructor Injection) und die erforderliche `TrackXXX()`-Methode aufrufen. Es wird nicht empfohlen, neue `TelemetryClient`-Instanzen zu erstellen. Eine Singletoninstanz von `TelemetryClient` ist bereits im `DependencyInjection`-Container registriert, der `TelemetryConfiguration` für alle sonstigen Telemetriedaten verwendet. Sie sollten nur dann eine neue `TelemetryClient`-Instanz erstellen, wenn für diese eine Konfiguration erforderlich ist, die sich von der für die sonstigen Telemetriedaten unterscheidet.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Kann ich die Visual Studio-IDE für das Onboarding von Application Insights in ein Workerdienstprojekt verwenden?

Das Onboarding mit der Visual Studio-IDE wird derzeit nur für ASP.NET-/ASP.NET Core-Anwendungen unterstützt. Dieses Dokument wird aktualisiert, wenn Visual Studio das Onboarding von Workerdienstanwendungen unterstützt.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Kann ich die Application Insights-Überwachung mit Tools wie dem Statusmonitor aktivieren?

Nein. Der [Statusmonitor](./monitor-performance-live-website-now.md) und der [Statusmonitor v2](./status-monitor-v2-overview.md) unterstützen aktuell ausschließlich ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Werden alle Features unterstützt, wenn ich meine Anwendung unter Linux ausführe?

Ja. Die Featureunterstützung für dieses SDK ist auf allen Plattformen gleich. Es gelten jedoch die folgenden Ausnahmen:

* Leistungsindikatoren werden nur unter Windows unterstützt, mit Ausnahme der Anzeige von Prozess-CPU/Arbeitsspeicher in Livemetriken.
* Auch wenn `ServerTelemetryChannel` standardmäßig aktiviert ist, wird über den Kanal bei der Anwendungsausführung unter Linux oder macOS nicht automatisch ein lokaler Speicherordner erstellt, um die Telemetriedaten bei Netzwerkproblemen vorübergehend zu speichern. Aufgrund dieser Einschränkung gehen Telemetriedaten verloren, wenn vorübergehende Netzwerk- oder Serverprobleme auftreten. Sie können das Problem umgehen, indem Sie einen lokalen Ordner für den Kanal konfigurieren:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Beispielanwendungen

[.NET Core-Konsolenanwendung](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Nutzen Sie dieses Beispiel, wenn Sie eine Konsolenanwendung verwenden, die in .NET Core (2.0 oder höher) oder .NET Framework (4.7.2 oder höher) geschrieben ist.

[ASP .NET Core-Hintergrundaufgaben mit gehosteten Diensten](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService) Nutzen Sie dieses Beispiel, wenn Sie in ASP.NET Core 2.1/2.2 Hintergrundaufgaben gemäß der offiziellen Anleitung[(hier) ](/aspnet/core/fundamentals/host/hosted-services) erstellen.

[.NET Core 3.0-Workerdienst](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Nutzen Sie dieses Beispiel, wenn Sie über eine .NET Core 3.0-Workerdienstanwendung gemäß der offiziellen Anleitung [(hier)](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio#worker-service-template) verfügen.

## <a name="open-source-sdk"></a>Open Source SDK

* [Lesen und Hinzufügen von Code](https://github.com/microsoft/ApplicationInsights-dotnet).

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](./release-notes.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden Sie die API](./api-custom-events-metrics.md), um Ihre eigenen Ereignisse und Metriken für eine detaillierte Ansicht der Leistung und Nutzung Ihrer App zu senden.
* [Nachverfolgen zusätzlicher Abhängigkeiten, die nicht automatisch nachverfolgt werden](./auto-collect-dependencies.md)
* [Anreichern oder Filtern erfasster Telemetriedaten](./api-filtering-sampling.md)
* [Dependency Injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)
