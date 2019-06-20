---
title: Azure Application Insights für ASP.NET Core | Microsoft-Dokumentation
description: Überwachen Sie ASP.NET Core-Webanwendungen auf Verfügbarkeit, Leistung und Auslastung.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: 2da253c058329b80c6175b9066b76816940dc3d5
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153987"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights für ASP.NET Core-Anwendungen

In diesem Artikel wird beschrieben, wie Sie Application Insights für eine [ASP.NET Core](https://docs.microsoft.com/aspnet/core)-Anwendung aktivieren. Wenn Sie die Anleitung in diesem Artikel befolgen, beginnt Application Insights damit, Anforderungen, Abhängigkeiten, Ausnahmen, Leistungsindikatoren, Heartbeats und Protokolle für Ihre ASP.NET Core-Anwendung zu erfassen. Die Beispielanwendung ist eine [MVC-Anwendung](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) mit einer Ausrichtung auf `netcoreapp2.2`, aber die Anleitung gilt für alle ASP.NET Core-Anwendungen.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

Mit dem [Application Insights SDK (Software Development Kit) für ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) können Ihre Anwendungen unabhängig davon überwacht werden, wo bzw. wie sie ausgeführt werden. Wenn Ihre Anwendung ausgeführt wird und über eine Netzwerkverbindung mit Azure verfügt, können Telemetriedaten gesammelt werden. Dies bedeutet, dass Application Insights-Überwachung überall dort unterstützt wird, wo .NET Core unterstützt wird. Diese Unterstützung gilt für alle Betriebssysteme (Windows, Linux, Mac), Hostingmethoden (In-Process/Out-of-Process), Bereitstellungsmethoden (frameworkabhängig/eigenständig), Webserver (IIS, Kestrel), Hostingplattformen (Azure-Web-Apps, Azure-VM, Docker, Azure Kubernetes Service (AKS) usw.) oder IDEs (Visual Studio, VS Code, Befehlszeile).

## <a name="prerequisites"></a>Voraussetzungen

- Eine funktionierende ASP.NET Core-Anwendung. Befolgen Sie dieses [Tutorial: Erste Schritte mit ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/), um bei Bedarf eine ASP.NET Core-Anwendung zu erstellen.
- Einen gültigen Application Insights-Instrumentierungsschlüssel, der zum Senden von Telemetriedaten an den Application Insights-Dienst benötigt wird. Befolgen Sie diese Anleitung zum [Erstellen einer Application Insights-Ressource](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource), um bei Bedarf eine neue Application Insights-Ressource zu erstellen und einen Instrumentierungsschlüssel zu beschaffen.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Aktivieren der serverseitigen Telemetrie für Application Insights (Visual Studio)

1. Öffnen Sie Ihr Projekt in Visual Studio.

    > [!TIP]
    > Obwohl kein obligatorischer Schritt, kann es hilfreich sein, die Quellcodeverwaltung für Ihr Projekt einzurichten, damit Sie alle Änderungen von Application Insights nachverfolgen können. Zum Aktivieren der Quellcodeverwaltung wählen Sie **Datei** > **Zur Quellcodeverwaltung hinzufügen** aus.

2. Wählen Sie **Projekt** > **Application Insights-Telemetrie hinzufügen** aus.

3. Wählen Sie **Erste Schritte** aus. (Je nach verwendeter Version von Visual Studio kann der Text geringfügig abweichen. Bei einigen älteren Versionen wird stattdessen die Schaltfläche **Kostenlos starten** angezeigt.)

4. Wählen Sie Ihr Abonnement und dann **Ressource** > **Registrieren** aus.

5. Überprüfen Sie nach dem Hinzufügen von Application Insights zu Ihrem Projekt, ob Sie das neueste stabile Release des SDK verwenden. Navigieren Sie zu **Projekt** > **NuGet-Pakete verwalten** > **Microsoft.ApplicationInsights.AspNetCore**, und wählen Sie ggf. **Aktualisieren** aus.

     ![Screenshot der Verwaltung des NuGet-Paketbildschirms mit für Update ausgewähltem Application Insights-Paket](./media/asp-net-core/update-nuget-package.png)

6. Wenn Sie den optionalen Tipp befolgt und Ihr Projekt der Quellcodeverwaltung hinzugefügt haben, können Sie zu **Ansicht** > **Team Explorer** > **Änderungen** gehen und jede einzelne Datei für eine Vergleichsansicht der Änderungen durch Hinzufügen der Application Insights-Telemetrie auswählen.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Aktivieren der serverseitigen Telemetrie für Application Insights (ohne Visual Studio)

1. Installieren Sie das [Application Insights SDK-NuGet-Paket für ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Sie sollten immer die neueste stabile Version verwenden. Vollständige Versionshinweise für das SDK finden Sie im [Open Source-GitHub-Repository](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Der folgende Codeausschnitt zeigt die Änderungen, die Sie der `.csproj`-Datei Ihres Projekts hinzufügen müssen.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Fügen Sie `services.AddApplicationInsightsTelemetry();` der `ConfigureServices()`-Methode in Ihrer `Startup`-Klasse hinzu. Unten ist das vollständige Beispiel angegeben.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. Richten Sie den Instrumentierungsschlüssel ein.

    Es ist zwar möglich, einen Instrumentierungsschlüssel als Argument für `AddApplicationInsightsTelemetry` bereitzustellen, aber Sie sollten den Instrumentierungsschlüssel in der Konfiguration angeben. Im Folgenden wird veranschaulicht, wie Sie in `appsettings.json` einen Instrumentierungsschlüssel angeben. Stellen Sie sicher, dass `appsettings.json` während der Veröffentlichung in den Stammordner der Anwendung kopiert wird.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Alternativ können Sie den Instrumentierungsschlüssel auch in einer der folgenden Umgebungsvariablen angeben.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Beispiel:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` wird normalerweise verwendet, um den Instrumentierungsschlüssel für Anwendungen anzugeben, die in Azure-Web-Apps bereitgestellt werden.

    > [!NOTE]
    > Ein Instrumentierungsschlüssel, der im Code angegeben ist, erhält Vorrang vor der Umgebungsvariablen `APPINSIGHTS_INSTRUMENTATIONKEY`, die wiederum Vorrang vor anderen Optionen hat.

## <a name="run-your-application"></a>Ausführen der Anwendung

 Führen Sie Ihre Anwendung aus, und senden Sie Anforderungen an die Anwendung. Die Telemetriedaten sollten nun an Application Insights fließen. Die folgenden Telemetriedaten werden vom Application Insights SDK automatisch erfasst.

|Anforderungen/Abhängigkeiten |Details|
|---------------|-------|
|Requests | Eingehende Webanforderungen für Ihre Anwendung. |
|Http/Https | Aufrufe mit `HttpClient`. |
|SQL | Aufrufe mit `SqlClient`. |
|[Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) | Aufrufe mit Azure Storage-Client. |
|[EventHub-Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 und höher. |
|[ServiceBus-Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 und höher. |
|Azure Cosmos DB | Wird nur dann automatisch nachverfolgt, wenn HTTP/HTTPS verwendet wird. Der TCP-Modus wird von Application Insights nicht erfasst. |

### <a name="performance-counters"></a>Leistungsindikatoren

Für die Unterstützung von [Leistungsindikatoren](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) in ASP.NET gelten die folgenden Beschränkungen:

   * SDK Version 2.4.1 und höher erfasst Leistungsindikatoren, wenn die Anwendung per Azure-Web-App (Windows) ausgeführt wird.
   * Mit SDK Version 2.7.0-beta3 und höher werden Leistungsindikatoren erfasst, wenn die Anwendung unter Windows ausgeführt wird und auf `NETSTANDARD2.0` oder höher ausgerichtet ist.
   * Für Anwendungen, die auf das .NET Framework abzielen, werden Leistungsindikatoren in allen Versionen des SDK unterstützt.
   * Dieser Artikel wird aktualisiert, wenn unter Linux die Unterstützung von Leistungsindikatoren hinzugefügt wird.

### <a name="ilogger-logs"></a>ILogger-Protokolle

[ILogger-Protokolle](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) mit dem Schweregrad `Warning` oder höher werden von SDK Version 2.7.0-beta3 oder höher automatisch erfasst.

### <a name="live-metrics"></a>Livemetriken

Es kann einige Minuten dauern, bis Telemetriedaten im Portal angezeigt werden. Um schnell zu überprüfen, ob alles funktioniert, sind [Livemetriken](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) am besten geeignet, während Anforderungen an der ausgeführten Anwendung vorgenommen werden.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Aktivieren der clientseitigen Telemetrie für Webanwendungen

Die obigen Schritte sind ausreichend, um mit dem Erfassen der serverseitigen Telemetriedaten zu beginnen. Wenn Ihre Anwendung über clientseitige Komponenten verfügt, sollten Sie die unten angegebenen Schritte ausführen, um dafür mit dem Erfassen von [Telemetriedaten zur Verwendung](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) zu beginnen.

1. Nehmen Sie in `_ViewImports.cshtml` eine Einfügung vor:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Fügen Sie in `_Layout.cshtml` HtmlHelper am Ende des Abschnitts `<head>` ein (vor allen anderen Skripts). Alle benutzerdefinierten JavaScript-Telemetriedaten, die Sie über die Seite melden möchten, sollten nach diesem Codeausschnitt eingefügt werden:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Die `.cshtml`-Dateinamen, auf die oben verwiesen wird, stammen aus einer Standardvorlage für die MVC-Anwendung. Um die clientseitige Überwachung für Ihre Anwendung ordnungsgemäß zu aktivieren, muss der JavaScript-Codeausschnitt im `<head>`-Abschnitt jeder Seite Ihrer Anwendung vorhanden sein, die Sie überwachen möchten. Für diese Anwendungsvorlage erreichen Sie dieses Ziel effektiv, wenn Sie den JavaScript-Codeausschnitt `_Layout.cshtml` hinzufügen. Wenn Ihr Projekt diese spezifische Datei nicht enthält, können Sie immer noch die [clientseitige Überwachung](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring) hinzufügen. Sie müssten entweder den JavaScript-Code einer entsprechenden Datei hinzufügen, die den `<head>` aller Seiten in Ihrer App steuert, oder könnten den Ausschnitt alternativ mehreren einzelnen Seiten hinzufügen, obwohl dies schwierig durchzuführen wäre und im Allgemeinen nicht zu empfehlen ist.

## <a name="configuring-application-insights-sdk"></a>Konfigurieren des Application Insights SDK

Das Application Insights SDK für ASP.NET Core kann angepasst werden, um die Standardkonfiguration zu ändern. Benutzer des Application Insights ASP.NET SDK sind ggf. mit der Konfiguration per `ApplicationInsights.config` oder per Änderung von `TelemetryConfiguration.Active` vertraut. Für ASP.NET Core wird die Konfiguration auf andere Art durchgeführt. Das ASP.NET Core-SDK wird der Anwendung hinzugefügt und mithilfe der integrierten [Abhängigkeitsinjektion](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) von ASP.NET Core konfiguriert. Fast alle Konfigurationsänderungen werden in der `ConfigureServices()`-Methode Ihrer `Startup.cs`-Klasse durchgeführt, falls nichts anderes angegeben ist. Die Abschnitte unten enthalten weitere Informationen hierzu.

> [!NOTE]
>  Eine Änderung der Konfiguration durch Modifizierung von `TelemetryConfiguration.Active` wird in ASP.NET Core-Anwendungen nicht empfohlen.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Konfigurieren mit ApplicationInsightsServiceOptions

Es ist möglich, einige allgemeine Einstellungen zu ändern, indem `ApplicationInsightsServiceOptions` an `AddApplicationInsightsTelemetry` übergeben wird. Ein entsprechendes Beispiel ist nachfolgend dargestellt.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

Die genaue Liste mit den konfigurierbaren Einstellungen in `ApplicationInsightsServiceOptions` finden Sie [hier](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>Stichproben

Das Application Insights SDK für ASP.NET Core unterstützt sowohl die FixedRate- als auch die adaptive Stichprobenerstellung. Die adaptive Stichprobenerstellung ist standardmäßig aktiviert. Mit unserer [Anleitung zur adaptiven Stichprobenerstellung](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) lernen Sie, die Stichprobenerstellung für ASP.NET Core-Anwendungen zu konfigurieren.

### <a name="adding-telemetryinitializers"></a>Hinzufügen von TelemetryInitializers

[Telemetrieinitialisierer](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) werden verwendet, wenn Sie die globalen Eigenschaften definieren möchten, die mit allen Telemetriedaten gesendet werden.

Fügen Sie das `TelemetryInitializer`-Element wie unten gezeigt in den DependencyInjection-Container ein, wenn Sie ein Element dieser Art hinzufügen möchten. `TelemetryInitializer`s, die dem DependencyInjection-Container hinzugefügt werden, werden vom SDK automatisch ausgewählt.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Entfernen von TelemetryInitializer-Elementen

Verwenden Sie zum Entfernen aller oder bestimmter TelemetryInitializer-Elemente, die standardmäßig vorhanden sind, den folgenden Beispielcode **nach** dem Aufruf von `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Hinzufügen von TelemetryProcessor-Elementen

Benutzerdefinierte Telemetrieprozessoren können `TelemetryConfiguration` hinzugefügt werden, indem die Erweiterungsmethode `AddApplicationInsightsTelemetryProcessor` in `IServiceCollection` verwendet wird. Telemetrieprozessoren werden in [erweiterten Filterszenarien](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) verwendet, um direktere Kontrolle darüber zu haben, was in die Telemetriedaten, die Sie an den Application Insights-Dienst senden, einbezogen oder davon ausgeschlossen wird. Verwenden Sie das folgende Beispiel.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurieren oder Entfernen von TelemetryModule-Standardelementen

Application Insights verwendet Telemetriemodule zum [automatischen Erfassen nützlicher Informationen](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) zu bestimmten Workloads, ohne dass zusätzliche Konfiguration erforderlich ist.

Die folgenden Module für die automatische Erfassung sind standardmäßig aktiviert und für die automatische Erfassung der Telemetriedaten verantwortlich. Sie können deaktiviert und konfiguriert werden, um das Standardverhalten zu ändern.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Verwenden Sie zum Konfigurieren von `TelemetryModule`-Standardelementen die Erweiterungsmethode `ConfigureTelemetryModule<T>` in `IServiceCollection`. Dies ist im Beispiel unten dargestellt.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurieren des Telemetriekanals

Der verwendete Standardkanal ist `ServerTelemetryChannel`. Er kann mithilfe des unten angegebenen Beispiels außer Kraft gesetzt werden.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Ich möchte zusätzlich zu den automatisch erfassten Telemetriedaten weitere Telemetriedaten nachverfolgen. Wie kann ich hierbei vorgehen?

Beschaffen Sie eine Instanz von `TelemetryClient`, indem Sie die Konstruktorinjektion nutzen und die erforderliche `TrackXXX()`-Methode aufrufen. Es wird nicht empfohlen, in der ASP.NET Core-Anwendung neue `TelemetryClient`-Instanzen zu erstellen, da eine Singletoninstanz von `TelemetryClient` bereits im DI-Container registriert ist, über den `TelemetryConfiguration` mit der restlichen Telemetrie gemeinsam genutzt wird. Die Erstellung einer neuen `TelemetryClient`-Instanz wird nur empfohlen, wenn dafür gegenüber der restlichen Telemetrie eine separate Konfiguration vorhanden sein muss. Im folgenden Beispiel wird veranschaulicht, wie Sie zusätzliche Telemetrie über einen Controller nachverfolgen.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Eine Beschreibung des Meldens von benutzerdefinierten Daten in Application Insights finden Sie unter [Application Insights-API für benutzerdefinierte Ereignisse und Metriken](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Für einige Visual Studio-Vorlagen wurde die Erweiterungsmethode „UseApplicationInsights()“ in IWebHostBuilder verwendet, um Application Insights zu aktivieren. Ist diese Verwendung noch gültig?

Die Aktivierung von Application Insights mit dieser Methode ist noch gültig und wird beim Visual Studio-Onboarding und auch für die Azure-Web-App-Erweiterungen verwendet. Es wird aber empfohlen, `services.AddApplicationInsightsTelemetry()` zu nutzen, da bei diesem Ansatz Überladungen zum Steuern der Konfiguration eingesetzt werden können. Beide Methoden bewirken intern dasselbe. Wenn keine benutzerdefinierte Konfiguration angewendet wird, ist also das Aufrufen beider Methoden in Ordnung.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Ich stelle meine ASP.NET Core-Anwendung in Azure-Web-Apps bereit. Sollte ich trotzdem die Application Insights-Erweiterung über Web-Apps aktivieren?

Wenn das SDK wie in diesem Artikel gezeigt zur Buildzeit installiert wird, ist es nicht erforderlich, die Application Insights-Erweiterung über das App Service-Portal zu aktivieren. Auch bei installierter Erweiterung werden keine Schritte ausgeführt, wenn erkannt wird, dass das SDK der Anwendung bereits hinzugefügt wurde. Durch das Aktivieren von Application Insights über die Erweiterung sparen Sie sich das Installieren und Aktualisieren des SDK. Die Aktivierung von Application Insights gemäß diesem Artikel bietet aus den unten genannten Gründen mehr Flexibilität.
   * Application Insights-Telemetrie funktioniert weiterhin unter:
       * allen Betriebssystemen (Windows, Linux, Mac).
       * allen Veröffentlichungsmodi (eigenständig oder frameworkabhängig).
       * allen Zielframeworks, z. B. vollständigem .NET Framework.
       * allen Hostingoptionen (Azure-Web-App, VMs, Linux, Container, AKS, außerhalb von Azure).
   * Die Telemetriedaten können beim Debuggen über Visual Studio lokal angezeigt werden.
   * Ermöglicht das Nachverfolgen von zusätzlichen benutzerdefinierten Telemetriedaten per `TrackXXX()`-API.
   * Die vollständige Steuerung der Konfiguration ist Ihnen überlassen.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>Kann ich die Application Insights-Überwachung mit Tools wie Statusmonitor aktivieren?

Nein. [Statusmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) und die neue Ersatzanwendung [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) unterstützen derzeit nur ASP.NET 4.x.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>Ich verwende eine ASP.NET Core 2.0-Anwendung. Ist Application Insights hierfür nicht automatisch aktiviert, ohne dass ich etwas tun muss?

`Microsoft.AspNetCore.All` Im Metapaket 2.0 war das Application Insights SDK (Version 2.1.0) enthalten. Wenn Sie die Anwendung unter dem Visual Studio-Debugger ausführen, wird Application Insights von Visual Studio aktiviert, und die Telemetriedaten werden lokal in der IDE angezeigt. Telemetriedaten wurden nicht an den Application Insights-Dienst gesendet, sofern nicht explizit ein Instrumentierungsschlüssel angegeben wurde. Wir empfehlen Ihnen, die Anleitungen in diesem Artikel zu befolgen, um Application Insights zu aktivieren (auch für 2.0-Apps).

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Ich führe meine Anwendung unter Linux aus. Werden alle Features auch unter Linux unterstützt?

* Ja. Die Featureunterstützung für das SDK ist auf allen Plattformen gleich. Es gelten lediglich die folgenden Ausnahmen:

    * Leistungsindikatoren werden außerhalb von Windows noch nicht unterstützt.
    * Auch wenn `ServerTelemetryChannel` standardmäßig aktiviert ist, wird über den Kanal bei der Anwendungsausführung in Linux oder MacOS nicht automatisch ein lokaler Speicherordner erstellt, um die Telemetriedaten bei Netzwerkproblemen vorübergehend zu speichern. Diese Einschränkung bewirkt, dass Telemetriedaten verloren gehen, wenn vorübergehende Netzwerk- oder Serverprobleme auftreten. Benutzer können dieses Problem umgehen, indem sie einen lokalen Ordner für den Kanal konfigurieren. Dies ist unten dargestellt.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>Open Source SDK
[Lesen und Hinzufügen von Code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Ein externes Video mit ausführlichen Informationen zum [Konfigurieren von Application Insights mit .NET Core und Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) von Grund auf.
- Ein externes Video mit ausführlichen Informationen zum [Konfigurieren von Application Insights mit .NET Core und Visual Studio Code](https://youtu.be/ygGt84GDync) von Grund auf

## <a name="next-steps"></a>Nächste Schritte
* [Untersuchen Sie Benutzerabläufe](../../azure-monitor/app/usage-flows.md), um die Benutzernavigation in Ihrer App nachzuvollziehen.
* [Konfigurieren Sie die Momentaufnahmensammlung](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger), um den Status des Quellcodes und der Variablen zu dem Zeitpunkt anzuzeigen, zu dem eine Ausnahme ausgelöst wurde.
* [Verwenden Sie die API](../../azure-monitor/app/api-custom-events-metrics.md) , um Ihre eigenen Ereignisse und Metriken für eine detailliertere Ansicht der Leistung und Nutzung Ihrer App zu senden.
* Überprüfen Sie Ihre App mit [Verfügbarkeitstests](../../azure-monitor/app/monitor-web-app-availability.md) fortwährend von jedem Ort der Welt aus.
