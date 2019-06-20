---
title: Filterung und Vorverarbeitung im Azure Application Insights-SDK | Microsoft Docs
description: Schreiben Sie Telemetrieprozessoren und Telemetrieinitialisierer für das SDK, um die Daten zu filtern oder ihnen Eigenschaften hinzuzufügen, bevor die Telemetriedaten an das Application Insights-Portal gesendet werden.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 1b55a2b053b86d3260fdca201357445d2556c444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60793972"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filterung und Vorverarbeitung von Telemetriedaten im Application Insights-SDK


Sie können Plug-Ins für das Application Insights SDK schreiben und konfigurieren, um anzupassen, wie Telemetriedaten erfasst und verarbeitet werden, bevor sie an den Application Insights-Dienst gesendet werden.

* [Erstellen von Stichproben](../../azure-monitor/app/sampling.md) verringert sich der Umfang der Telemetriedaten, ohne Statistiken zu verfälschen. Zusammengehörige Datenpunkte werden dabei zusammengehalten, sodass Sie bei der Diagnose von Problemen zwischen diesen navigieren können. Im Portal wird die Gesamtanzahl multipliziert, um eine Kompensation der Stichproben zu erreichen.
* Durch das Filtern mit Telemetrieprozessoren für [ASP.NET](#filtering) oder [Java](../../azure-monitor/app/java-filter-telemetry.md) können Sie Telemetriedaten im SDK auswählen oder ändern, bevor sie an den Server gesendet werden. Sie können beispielsweise den Umfang der Telemetriedaten verringern, indem Sie Anforderungen von Robots ausschließen. Filtern stellt jedoch ein grundlegenderes Verfahren zur Senkung des Datenverkehrs dar als das Erstellen von Stichproben. Sie können so besser steuern, was übertragen wird. Jedoch müssen Sie beachten, dass dies Auswirkungen auf die Statistik hat – wenn Sie z. B. alle erfolgreichen Anforderungen herausfiltern.
* [Telemetrie-Initialisierer](#add-properties) fügen Eigenschaften zu beliebigen von der App gesendeten Telemetriedaten hinzu, einschließlich Telemetriedaten von Standardmodulen. Sie könnten z. B. berechnete Werte hinzufügen oder Versionsnummern, nach denen Sie die Daten im Portal filtern.
* [SDK-API](../../azure-monitor/app/api-custom-events-metrics.md) wird zum Senden benutzerdefinierter Ereignisse und Metriken verwendet.

Vorbereitung:

* Installieren Sie das Application Insights [SDK für ASP.NET](../../azure-monitor/app/asp-net.md) oder [SDK für Java](../../azure-monitor/app/java-get-started.md) in Ihrer App.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtern: ITelemetryProcessor
Diese Technik bietet Ihnen eine bessere Kontrolle über die Daten aus dem Telemetriedatenstrom, die ein- oder ausgeschlossen werden. Sie können sie zusammen mit der Stichprobenerstellung oder einzeln verwenden.

Zum Filtern von Telemetriedaten schreiben Sie einen Telemetrie-Prozessor und registrieren ihn beim SDK. Alle Telemetriedaten durchlaufen den Prozessor. Sie können dann auswählen, diese aus dem Datenstrom zu verwerfen, oder Eigenschaften hinzufügen. Dies schließt Telemetriedaten aus Standardmodulen wie etwa dem HTTP-Anforderungssammler und der Abhängigkeitserfassung sowie von Ihnen erstellte Telemetriedaten ein. Sie können z. B. Telemetriedaten zu Anforderungen von Robots oder erfolgreiche Abhängigkeitsaufrufe herausfiltern.

> [!WARNING]
> Die Filterung der vom SDK gesendeten Telemetriedaten mithilfe von Prozessoren kann die im Portal angezeigten Statistiken verfälschen und die Nachverfolgung verwandter Elemente erschweren.
>
> Verwenden Sie stattdessen [Sampling](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Erstellen eines Telemetrieprozessors (C#)
1. Vergewissern Sie sich, dass in Ihrem Projekt das Application Insights SDK, Version 2.0.0 oder höher, verwendet wird. Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „NuGet-Pakete verwalten“ aus. Aktivieren Sie im NuGet-Paket-Manager „Microsoft.ApplicationInsights.Web“.
2. Implementieren Sie zum Erstellen eines Filters „ITelemetryProcessor“. Hierbei handelt es sich um einen weiteren Erweiterungspunkt wie Telemetriemodul, Telemetrieinitialisierer und Telemetriekanal.

    Beachten Sie, dass Telemetrieprozessoren eine Verarbeitungskette erstellen. Beim Instanziieren eines Telemetrieprozessors übergeben Sie einen Link an den nächsten Prozessor in der Kette. Wenn ein Telemetriedatenpunkt an die Verarbeitungsmethode übergeben wird, führt er seine Aufgaben aus und ruft dann den nächsten Telemetrieprozessor in der Kette auf.

    ```csharp

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
3. Fügen Sie Folgendes in „ApplicationInsights.config“ ein:

```xml

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Dies ist der gleiche Abschnitt, in dem Sie einen Filter für die Stichprobe initialisieren.).

Sie können durch die Bereitstellung von öffentlich benannten Eigenschaften in Ihrer Klasse Zeichenfolgenwerte aus der .config-Datei übergeben.

> [!WARNING]
> Achten Sie darauf, dass die Typnamen und sonstige Eigenschaftennamen in der .config-Datei mit den Klassen- und Eigenschaftennamen im Code übereinstimmen. Wenn die .config-Datei auf einen nicht vorhandenen Typ oder eine nicht vorhandene Eigenschaft verweist, könnte das SDK möglicherweise automatisch keine Telemetriedaten mehr senden.
>
>

**Alternativ** können Sie den Filter im Code initialisieren. Fügen Sie in einer geeigneten Initialisierungsklasse, z. B. AppStart in „Global.asax.cs“ Ihren Prozessor in die Kette ein:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Nach diesem Punkt erstellte TelemetryClients-Elemente verwenden Ihre Prozessoren.

### <a name="example-filters"></a>Beispielfilter
#### <a name="synthetic-requests"></a>Synthetische Anforderungen
Filtern Sie Bots und Webtests heraus. Mit dem Metrik-Explorer haben Sie die Option, synthetische Quellen herauszufiltern. Dadurch wird der Datenverkehr verringert, da die Quellen im SDK gefiltert werden.

```csharp

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Fehler bei der Authentifizierung
Filtern Sie Abfragen mit der Antwort „401“ heraus.

```csharp

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Herausfiltern schneller Remoteabhängigkeitsaufrufe
Wenn Sie langsame Aufrufe diagnostizieren möchten, filtern Sie die schnellen Aufrufe heraus.

> [!NOTE]
> Dadurch wird die im Portal angezeigte Statistik verfälscht. Das Abhängigkeitsdiagramm sieht aus, als ob alle Abhängigkeitsaufrufe fehlgeschlagen wären.
>
>

```csharp

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnostizieren von Abhängigkeitsproblemen
[diesem Blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) wird ein Projekt zur Diagnose von Abhängigkeitsproblemen beim automatischen Senden regulärer Pings an Abhängigkeiten beschrieben.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Hinzufügen von Eigenschaften: ITelemetryInitializer
Mithilfe von Telemetrieinitialisierern definieren Sie globale Eigenschaften, die mit allen Telemetriedaten gesendet werden, und setzen das ausgewählte Verhalten der Standardtelemetriemodule außer Kraft.

Das Application Insights for Web-Paket erfasst beispielsweise Telemetriedaten über HTTP-Anforderungen. Standardmäßig kennzeichnet es jede Anforderung mit einem Antwortcode >= 400 als fehlerhaft. Wenn 400 jedoch als erfolgreich behandelt werden soll, können Sie einen Telemetrieinitialisierer angeben, der die Success-Eigenschaft festlegt.

Wenn Sie einen Telemetrieinitialisierer angeben, wird dieser immer aufgerufen, wenn eine der Track*()-Methoden aufgerufen wird. Dies umfasst auch Methoden, die von den Standardtelemetriemodulen aufgerufen werden. Nach Abmachung legen diese Module keine Eigenschaft fest, die bereits durch einen Initialisierer festgelegt wurde.

**Definieren des Initialisierers**

*C#*

```csharp

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Laden des Initialisierers**

In "ApplicationInsights.config":

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

*Alternativ* können Sie den Initialisierer im Code instanziieren, z.B. in „Global.aspx.cs“:

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Weitere Informationen zu diesem Beispiel anzeigen.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="java-telemetry-initializers"></a>Java-Telemetrieinitialisierer

[Java SDK-Dokumentation](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Registrieren Sie anschließend den benutzerdefinierten Initialisierer in der Datei „applicationinsights.xml“.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
<Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript-Telemetrieinitialisierer
*JavaScript*

Fügen Sie einen Telemetrieinitialisierer unmittelbar nach dem Initialisierungscode ein, den Sie vom Portal abgerufen haben:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item�s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Eine Übersicht der für „telemetryItem“ verfügbaren nicht benutzerdefinierten Eigenschaften finden Sie im [Application Insights-Datenmodell für den Export von Daten](../../azure-monitor/app/export-data-model.md).

Sie können beliebig viele Initialisierer hinzufügen.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor und ITelemetryInitializer
Was ist der Unterschied zwischen Telemetrieprozessoren und Telemetrieinitialisierern?

* Die Einsatzmöglichkeiten überlappen sich teilweise: Beide können verwendet werden, um einer Telemetrie Eigenschaften hinzuzufügen.
* Telemetrieinitialisierer werden immer vor Telemetrieprozessoren ausgeführt.
* Mit Telemetrieprozessoren können Sie ein Telemetrieelement vollständig ersetzen oder verwerfen.
* Telemetrieprozessoren verarbeiten keine Telemetrie von Leistungsindikatoren.

## <a name="troubleshooting-applicationinsightsconfig"></a>Behandeln von Problemen mit „ApplicationInsights.config“
* Vergewissern Sie sich, dass der vollqualifizierte Typname und der Assemblyname korrekt sind.
* Vergewissern Sie sich, dass sich die Datei „applicationinsights.config“ in Ihrem Ausgabeverzeichnis befindet und auf dem neuesten Stand ist.

## <a name="reference-docs"></a>Referenz
* [API-Übersicht](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET-Referenz](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-Code
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Nächste Schritte
* [Durchsuchen von Ereignissen und Protokollen](../../azure-monitor/app/diagnostic-search.md)
* [Stichproben](../../azure-monitor/app/sampling.md)
* [Problembehandlung](../../azure-monitor/app/troubleshoot-faq.md)
