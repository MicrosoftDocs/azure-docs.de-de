---
title: Untersuchen von .NET-Ablaufverfolgungsprotokollen in Azure Application Insights mit ILogger
description: Beispiele für die Verwendung des Azure Application Insights-ILogger-Anbieters mit ASP.NET Core- und Konsolenanwendungen.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fd5a16334fff0319d7993fb2403a48d1777f6bce
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955338"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider für .NET Core-ILogger-Protokolle

ASP.NET Core unterstützt eine Protokollierungs-API, die mit verschiedenen Arten von integrierten und Drittanbieter-Protokollierungsfunktionen funktioniert. Die Protokollierung erfolgt über einen Aufruf von **Log()** oder einer Variante davon in *ILogger*-Instanzen. In diesem Artikel wird veranschaulicht, wie Sie mit *ApplicationInsightsLoggerProvider* ILogger-Protokolle in der Konsole und ASP.NET Core-Anwendungen erfassen. Darüber hinaus wird beschrieben, wie ApplicationInsightsLoggerProvider in andere Application Insights-Telemetrie integriert wird.
Weitere Informationen finden Sie unter [Protokollierung in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core-Anwendungen

ApplicationInsightsLoggerProvider wird in [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Version 2.7.0-beta3 (und höher) standardmäßig aktiviert, wenn Sie die normale Application Insights-Überwachung über eine der Standardmethoden aktivieren:
- Durch Aufrufen der **UseApplicationInsights**-Erweiterungsmethode für IWebHostBuilder 
- Durch Aufrufen der **AddApplicationInsightsTelemetry**-Erweiterungsmethode für IServiceCollection

Von ApplicationInsightsLoggerProvider erfasste ILogger-Protokolle unterliegen der gleichen Konfiguration wie alle sonstigen erfassten Telemetriedaten. Sie weisen dieselben TelemetryInitializers und TelemetryProcessors auf, verwenden denselben TelemetryChannel, und die Korrelation und Stichprobenentnahme erfolgt wie bei anderen Telemetriedaten. Wenn Sie Version 2.7.0-beta3 oder höher verwenden, müssen Sie zum Erfassen von ILogger-Protokollen keine Aktion ausführen.

Nur ILogger-Protokolle vom Typ *Warnung* oder höher (aus allen Kategorien) werden standardmäßig an Application Insights gesendet. Sie können jedoch [Filter anwenden, um dieses Verhalten zu ändern](#control-logging-level). Zum Erfassen von ILogger-Protokollen aus **Program.cs** oder **Startup.cs** sind weitere Schritte erforderlich. (Siehe [Erfassen von ILogger-Protokollen aus „Startup.cs“ und „Program.cs“ in ASP.Net Core-Anwendungen](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Wenn Sie eine frühere Version des Microsoft.ApplicationInsights.AspNet SDK verwenden oder nur ApplicationInsightsLoggerProvider ohne jede andere Application Insights-Überwachung verwenden möchten, führen Sie folgenden Vorgang aus:

1. Installieren Sie das NuGet-Paket:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Ändern Sie **Program.cs** wie hier veranschaulicht:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Mit dem Code in Schritt 2 wird `ApplicationInsightsLoggerProvider` konfiguriert. Im folgenden Code wird eine Controller-Beispielklasse gezeigt, die Protokolle mit `ILogger` sendet. Die Protokolle werden von Application Insights erfasst.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Erfassen von ILogger-Protokollen aus „Startup.cs“ und „Program.cs“ in ASP.NET Core-Anwendungen

Der neue ApplicationInsightsLoggerProvider kann Protokolle zu einem frühen Zeitpunkt in der Startpipeline der Anwendung erfassen. ApplicationInsightsLoggerProvider wird in Application Insights (ab Version 2.7.0-beta3) zwar automatisch aktiviert, ein Instrumentierungsschlüssel wird jedoch erst später in der Pipeline eingerichtet. Daher werden nur Protokolle aus der **Controller**-Klasse/anderen Klassen erfasst. Um alle Protokolle, beginnend mit **Program.cs** und **Startup.cs** selbst, zu erfassen, müssen Sie einen Instrumentierungsschlüssel für ApplicationInsightsLoggerProvider explizit aktivieren. Außerdem ist *TelemetryConfiguration* nicht komplett eingerichtet, wenn Protokolle aus **Program.cs** oder **Startup.cs** selbst erfasst werden. Deshalb weisen diese Protokolle eine minimale Konfiguration auf, die InMemoryChannel verwendet, es erfolgt keine Stichprobenentnahme, und es gibt keine standardmäßigen Telemetrieinitialisierer oder -prozessoren.

In den folgenden Beispielen wird diese Funktion mit **Program.cs** und **Startup.cs** veranschaulicht.

#### <a name="example-programcs"></a>Beispiel für „Program.cs“

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Beispiel für „Startup.cs“

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrieren vom alten ApplicationInsightsLoggerProvider

Frühere Versionen des Microsoft.ApplicationInsights.AspNet SDK als 2.7.0-beta2 unterstützten einen Protokollierungsanbieter, der nun veraltet ist. Dieser Anbieter wurde durch die **AddApplicationInsights()**-Erweiterungsmethode von ILoggerFactory aktiviert. Es wird empfohlen, dass Sie zum neuen Anbieter migrieren. Dazu müssen zwei Schritte ausgeführt werden:

1. Entfernen Sie den Aufruf von *ILoggerFactory.AddApplicationInsights()* aus der **Startup.Configure()**-Methode, um eine doppelte Protokollierung zu vermeiden.
2. Wenden Sie Filterregeln im Code neu an, da sie vom neuen Anbieter nicht berücksichtigt werden. Überladungen von *ILoggerFactory.AddApplicationInsights()* erforderten mindestens LogLevel- oder Filterfunktionen. Beim neuen Anbieter ist das Filtern Teil des Protokollierungsframeworks selbst. Es wird nicht vom Application Insights-Anbieter ausgeführt. Daher sollten alle durch *ILoggerFactory.AddApplicationInsights()*-Überladungen bereitgestellte Filter entfernt werden. Befolgen Sie beim Angeben von Filterregeln die Anweisungen in [Steuern des Protokolliergrads](#control-logging-level). Wenn Sie *appsettings.json* zum Filtern der Protokollierung verwenden, funktioniert dies auch beim neuen Anbieter, da beide den gleichen Anbieteralias *ApplicationInsights* verwenden.

Sie können weiterhin den alten-Anbieter verwenden. (Er wird erst bei einer Umstellung der Hauptversion auf 3.*xx* entfernt.) Wir empfehlen jedoch aus folgenden Gründen, dass Sie zum neuen Anbieter migrieren:

- Der vorherige Anbieter unterstützt keine [Protokollbereiche](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Beim neuen Anbieter werden Eigenschaften aus dem Bereich den gesammelten Telemetriedaten automatisch als benutzerdefinierte Eigenschaften hinzugefügt.
- Protokolle können jetzt zu einem viel früheren Zeitpunkt in der Startpipeline der Anwendung erfasst werden, Protokolle können jetzt aus den Klassen **Program** und **Startup** erfasst werden.
- Beim neuen Anbieter erfolgt die Filterung auf der Frameworkebene. Sie können Protokolle für den Application Insights-Anbieter auf die gleiche Weise wie für andere Anbieter filtern, einschließlich integrierte Anbieter wie Console, Debug usw. Sie können auch dieselben Filter auf mehrere Anbieter anwenden.
- In ASP.NET Core (2.0 und höher) wird zum [Aktivieren von Protokollierungsanbietern](https://github.com/aspnet/Announcements/issues/255) empfohlen, Erweiterungsmethoden für ILoggingBuilder in **Program.cs** selbst zu verwenden.

> [!Note]
> Der neue Anbieter ist für Anwendungen verfügbar, die auf NETSTANDARD2.0 oder höher ausgelegt sind. Wenn Ihre Anwendung auf ältere .NET Core-Versionen wie .NET Core 1.1 oder das .NET Framework ausgelegt ist, verwenden Sie weiterhin den alten Anbieter.

## <a name="console-application"></a>Konsolenanwendung

Im folgenden Code sehen Sie ein Beispiel für eine Konsolenanwendung, die so konfiguriert wurde, dass ILogger-Ablaufverfolgungen an Application Insights gesendet werden.

Installierte Pakete:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

In diesem Beispiel wird das eigenständige Paket `Microsoft.Extensions.Logging.ApplicationInsights` verwendet. Standardmäßig verwendet diese Konfiguration für TelemetryConfiguration das absolute Minimum zum Senden von Daten an Application Insights. Absolutes Minimum bedeutet, dass als Kanal InMemoryChannel verwendet wird. Es erfolgt keine Stichprobenentnahme, und es gibt keine standardmäßigen TelemetryInitializers. Dieses Verhalten kann für eine Konsolenanwendung außer Kraft gesetzt werden. Dies wird im folgenden Beispiel gezeigt.

Installieren Sie dieses zusätzliche Paket:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Im folgenden Abschnitt wird gezeigt, wie die standardmäßige TelemetryConfiguration mit der **services.Configure<TelemetryConfiguration>()**-Methode außer Kraft gesetzt wird. In diesem Beispiel werden `ServerTelemetryChannel` und die Stichprobenentnahme eingerichtet. Der TelemetryConfiguration wird ein benutzerdefinierter ITelemetryInitializer hinzugefügt.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Steuern des Protokolliergrads

Die *ILogger*-Infrastruktur von ASP.NET Core enthält einen integrierten Mechanismus zum Anwenden der [Protokollfilterung](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Damit können Sie die Protokolle steuern, die an jeden registrierten Anwender gesendet werden, einschließlich des Application Insights-Anbieters. Die Filterung kann entweder in der Konfiguration (normalerweise mit einer Datei *appsettings.json*) oder im Code erfolgen. Diese Funktion wird durch das Framework selbst bereitgestellt. Sie ist nicht spezifisch für den Application Insights-Anbieter.

In den folgenden Beispielen werden Filterregeln auf ApplicationInsightsLoggerProvider angewendet.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Erstellen von Filterregeln in der Konfiguration mit „appsettings.json“

Für ApplicationInsightsLoggerProvider lautet der Anbieteralias `ApplicationInsights`. Der folgende Abschnitt von *appsettings.json* konfiguriert, dass Protokolle für *Warnung* und höher aus allen Kategorien und *Fehler* und höher aus Kategorien, die mit „Microsoft“ beginnen, an `ApplicationInsightsLoggerProvider` gesendet werden.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Erstellen von Filterregeln im Code

Der folgende Codeausschnitt konfiguriert, dass Protokolle für *Warnung* und höher aus allen Kategorien und für *Fehler* und höher aus Kategorien, die mit „Microsoft“ beginnen, an `ApplicationInsightsLoggerProvider` gesendet werden. Diese Konfiguration ist identisch mit der im vorigen Abschnitt in *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Welche ist die alte und welche die neue Version von ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthielt einen integrierten ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), der über **ILoggerFactory**-Erweiterungsmethoden aktiviert wurde. Dieser Anbieter ist seit Version 2.7.0-beta2 als veraltet markiert. Er wird mit der Umstellung auf die nächste Hauptversion komplett entfernt. Das Paket [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) selbst ist nicht veraltet. Es wird benötigt zum Überwachen von Anforderungen, Abhängigkeiten usw.

Die empfohlene Alternative ist das neue eigenständige Paket [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), das einen verbesserten ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) und Erweiterungsmethoden in ILoggerBuilder für dessen Aktivierung enthält.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Version 2.7.0-beta3 ist vom neuen Paket abhängig und aktiviert die ILogger-Erfassung automatisch.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Warum werden einige ILogger-Protokolle in Application Insights zweimal angezeigt?

Duplizierung kann auftreten, wenn die ältere (nun veraltete) Version von ApplicationInsightsLoggerProvider durch Aufrufen von `AddApplicationInsights` für `ILoggerFactory` aktiviert wurde. Überprüfen Sie, ob Ihre **Configure**-Methode Folgendes enthält, und entfernen Sie den Eintrag:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Wenn Sie beim Debuggen aus Visual Studio doppelte Protokollierung feststellen, legen Sie im Code, der Application Insights aktiviert, `EnableDebugLogger` wie folgt auf *false* fest. Diese Duplizierung und die Behebung des Problems betreffen nur das Debuggen der Anwendung.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ich habe ein Update auf [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Version 2.7.0-beta3 durchgeführt, und Protokolle aus ILogger werden automatisch erfasst. Wie kann ich dieses Feature vollständig deaktivieren?

Allgemeine Informationen zum Filtern von Protokollen finden Sie im Abschnitt [Steuern des Protokolliergrads](../../azure-monitor/app/ilogger.md#control-logging-level). Zum Deaktivieren von ApplicationInsightsLoggerProvider verwenden Sie `LogLevel.None`:

**Im Code:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**In der Konfiguration:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Warum weisen einige ILogger-Protokolle nicht dieselben Eigenschaften wie andere auf?

Application Insights erfasst und sendet ILogger-Protokolle mit derselben TelemetryConfiguration, die auch für andere Telemetriedaten verwendet wird. Es gibt jedoch eine Ausnahme. In der Standardeinstellung ist TelemetryConfiguration nicht vollständig eingerichtet, wenn Sie aus **Program.cs** oder **Startup.cs** protokollieren. Protokolle aus diesen Quellen weisen nicht die Standardkonfiguration auf, daher führen sie nicht alle TelemetryInitializers und TelemetryProcessors aus.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Ich verwende das eigenständige Paket „Microsoft.Extensions.Logging.ApplicationInsights“ und möchte einige zusätzliche benutzerdefinierte Telemetriedaten manuell protokollieren. Wie sollte ich dazu vorgehen?

Bei Verwendung des eigenständigen Pakets wird `TelemetryClient` nicht in den DI-Container eingefügt. Deshalb müssen Sie eine neue Instanz von `TelemetryClient` erstellen und die gleiche Konfiguration wie der Protokollanbieter verwenden. Dies wird im folgenden Code veranschaulicht. So wird sichergestellt, dass dieselbe Konfiguration für alle benutzerdefinierten Telemetriedaten sowie für die Telemetriedaten von ILogger verwendet wird.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Wenn Sie Application Insights mit dem Paket Microsoft.ApplicationInsights.AspNetCore aktivieren, ändern Sie diesen Code, um `TelemetryClient` direkt in den Konstruktor abzurufen. Ein Beispiel finden Sie in diesen [häufig gestellten Fragen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Welcher Typ von Application Insights-Telemetriedaten wird anhand von ILogger-Protokollen erstellt? Wo kann ich ILogger-Protokolle in Application Insights einsehen?

ApplicationInsightsLoggerProvider erfasst ILogger-Protokolle und erstellt daraus TraceTelemetry. Wenn ein Ausnahmeobjekt an die **Log()**-Methode in ILogger übergeben wird, wird anstelle einer TraceTelemetry eine *ExceptionTelemetry* erstellt. Diese Telemetrieelemente sind an denselben Orten wie jede andere TraceTelemetry oder ExceptionTelemetry für Application Insights zu finden, einschließlich Portal, Analysen oder lokalem Visual Studio-Debugger.

Wenn Sie immer TraceTelemetry senden möchten, verwenden Sie diesen Codeausschnitt: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Ich habe das SDK nicht installiert und verwende die Azure-Web-Apps-Erweiterung, um Application Insights für meine ASP.NET Core-Anwendungen zu aktivieren. Wie verwende ich den neuen Anbieter? 

Die Application Insights-Erweiterung in Azure-Web-Apps verwendet den alten Anbieter. Sie können die Filterregeln in der Datei *appsettings.json* für die Anwendung ändern. Wenn Sie den neuen Anbieter nutzen möchten, verwenden Sie die Instrumentierung zur Buildzeit, indem Sie eine NuGet-Abhängigkeit für das SDK anwenden. Dieser Artikel wird aktualisiert, wenn die Erweiterung auf die Verwendung des neuen Anbieters umgestellt wird.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Ich verwende das eigenständige Paket Microsoft.Extensions.Logging.ApplicationInsights und aktiviere den Application Insights-Anbieter durch Aufruf von **builder.AddApplicationInsights("ikey")**. Gibt es eine Möglichkeit, einen Instrumentierungsschlüssel aus der Konfiguration abzurufen?


Ändern Sie die Dateien „program.cs“ und „appsettings.json“ wie folgt:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Entsprechender Abschnitt aus `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Dieser Code ist nur bei Verwendung eines eigenständigen Protokollierungsanbieters erforderlich. Bei der normalen Application Insights-Überwachung wird der Instrumentierungsschlüssel automatisch aus dem Konfigurationspfad *ApplicationInsights: Instrumentationkey* geladen. Die Datei „appsettings.json“ sollte wie folgt aussehen:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

* [Protokollierung in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [.NET-Ablaufverfolgungsprotokolle in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
