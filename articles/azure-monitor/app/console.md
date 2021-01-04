---
title: Azure Application Insights für Konsolenanwendungen | Microsoft-Dokumentation
description: Überwachen Sie Webanwendungen auf Verfügbarkeit, Leistung und Auslastung.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: aa39a1eca04621fc4db75f755402d3679403e814
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920593"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights für .NET-Konsolenanwendungen

Mit [Application Insights](./app-insights-overview.md) können Sie Ihre Webanwendung auf Verfügbarkeit, Leistung und Nutzung überwachen.

Sie benötigen ein Abonnement für [Microsoft Azure](https://azure.com). Melden Sie sich mit einem Microsoft-Konto an, das Sie möglicherweise für Windows, Xbox Live oder andere Microsoft-Clouddienste verwenden. Falls Ihr Team über ein Unternehmensabonnement für Azure verfügt, können Sie den Besitzer bitten, Sie über Ihr Microsoft-Konto hinzuzufügen.

> [!NOTE]
> Es wird *dringend empfohlen*, das [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)-Paket und die zugehörigen Anweisungen [hier](./worker-service.md) für alle Konsolenanwendungen zu verwenden. Dieses Paket hat [`NetStandard2.0`](/dotnet/standard/net-standard) zum Ziel und kann daher in .NET Core 2.1 oder höher und .NET Framework 4.7.2 oder höher verwendet werden.

## <a name="getting-started"></a>Erste Schritte

> [!IMPORTANT]
> Neue Azure-Regionen **erfordern** die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. Die [Verbindungszeichenfolge](./sdk-connection-string.md?tabs=net) identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.

* Erstellen Sie im [Azure-Portal](https://portal.azure.com)[eine Application Insights-Ressource](./create-new-resource.md). Wählen Sie für den Typ **Allgemein** aus.
* Erstellen Sie eine Kopie des Instrumentierungsschlüssels. Diesen finden Sie in der Dropdownliste **Essentials** der neuen Ressource, die Sie erstellt haben.
* Installieren Sie das neueste [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights)-Paket.
* Legen Sie den Instrumentierungsschlüssel im Code fest, bevor Sie Telemetriedaten nachverfolgen (oder legen Sie die Umgebungsvariable APPINSIGHTS_INSTRUMENTATIONKEY fest). Anschließend sollten Sie Telemetriedaten manuell nachverfolgen und im Azure-Portal anzeigen können.

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetriedaten werden nicht sofort gesendet. Telemetrieelemente werden durch das Application Insights SDK zusammengefasst und gesendet. In Konsolen-Apps, die direkt nach dem Aufrufen von `Track()`-Methoden beendet werden, werden Telemetriedaten unter Umständen nur gesendet, wenn `Flush()` und `Sleep`/`Delay` vor der App-Beendigung ausgeführt werden, wie in dem [vollständigen Beispiel](#full-example) weiter unten in diesem Artikel gezeigt. `Sleep` ist nicht erforderlich, wenn Sie `InMemoryChannel` verwenden. Es gibt ein aktives Problem bezüglich der Notwendigkeit von `Sleep`, das hier nachverfolgt wird: [ApplicationInsights-dotnet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Installieren Sie die neueste Version des Pakets [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector). Es verfolgt automatisch HTTP-, SQL- und einige andere externe Abhängigkeitsaufrufe.

Sie können Application Insights im Code initialisieren und konfigurieren oder dazu die Datei `ApplicationInsights.config` verwenden. Stellen Sie sicher, dass die Initialisierung so früh wie möglich erfolgt. 

> [!NOTE]
> Anweisungen für **ApplicationInsights.config** gelten nur für Apps, die auf .NET Framework abzielen. Sie gelten nicht für .NET Core-Anwendungen.

### <a name="using-config-file"></a>Verwenden der Konfigurationsdatei

Standardmäßig sucht das Application Insights SDK im Arbeitsverzeichnis nach der Datei `ApplicationInsights.config`, wenn `TelemetryConfiguration` erstellt wird.

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Sie können den Pfad zu der Konfigurationsdatei aber auch angeben.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Weitere Informationen finden Sie in der [Referenz zu Konfigurationsdateien](configuration-with-applicationinsights-config.md).

Ein vollständiges Beispiel der Konfigurationsdatei erhalten Sie, wenn Sie die neueste Version des Pakets [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) installieren. Dies ist die **minimale** Konfiguration für die Abhängigkeitssammlung, die dem Codebeispiel entspricht.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurieren der Telemetrieerfassung im Code
> [!NOTE]
> Das Lesen von Konfigurationsdateien wird unter .NET Core nicht unterstützt. Unter Umständen ist es ratsam, das [Application Insights-SDK für ASP.NET Core](./asp-net-core.md) zu verwenden.

* Erstellen und konfigurieren Sie während des Starts der Anwendung die `DependencyTrackingTelemetryModule`-Instanz – sie muss ein Singleton sein und für die Lebensdauer der Anwendung beibehalten werden.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Hinzufügen häufiger Telemetrieinitialisierer

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Wenn Sie die Konfiguration mit dem einfachen `TelemetryConfiguration()`-Konstruktor erstellt haben, müssen Sie außerdem Korrelationsunterstützung aktivieren. **Dies ist nicht erforderlich,** wenn Sie die Konfiguration aus einer Datei lesen oder `TelemetryConfiguration.CreateDefault()` oder `TelemetryConfiguration.Active` verwendet haben.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Sie können auch das Leistungsindikator-Sammlermodul installieren und initialisieren. Eine Beschreibung finden Sie [hier](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/).


#### <a name="full-example"></a>Vollständiges Beispiel

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen Sie Abhängigkeiten](./asp-net-dependencies.md), um zu überprüfen, ob REST-, SQL- oder andere externe Ressourcen die Leistung beeinträchtigen.
* [Verwenden Sie die API](./api-custom-events-metrics.md) , um Ihre eigenen Ereignisse und Metriken für eine detailliertere Ansicht der Leistung und Nutzung Ihrer App zu senden.

