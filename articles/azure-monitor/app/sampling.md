---
title: Stichprobenerstellung für Telemetriedaten in Azure Application Insights | Microsoft-Dokumentation
description: So behalten Sie die Kontrolle über die Menge an erfassten Telemetriedaten.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: e9334d222d443679362514481ecd83b90bbda0ac
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855072"
---
# <a name="sampling-in-application-insights"></a>Erstellen von Stichproben in Application Insights

Die Stichprobenerstellung ist eine Funktion in [Azure Application Insights](./app-insights-overview.md). Sie ist die empfohlene Methode zum Reduzieren von Telemetriedatenverkehr, Datenkosten und Speicherkosten, während gleichzeitig eine statistisch korrekte Analyse der Anwendungsdaten sichergestellt wird. Die Stichprobenerstellung hilft auch, eine Drosselung der Telemetriedaten durch Application Insights zu vermeiden. Der Stichprobenfilter wählt verwandte Elemente aus, sodass Sie beim Ausführen diagnostischer Untersuchungen zwischen Elementen navigieren können.

Bei der Anzeige im Portal werden die Metrikergebnisse zur Berücksichtigung der Stichprobenerstellung renormalisiert. Dadurch werden die Auswirkungen auf die Statistiken minimiert.

## <a name="brief-summary"></a>Kurze Zusammenfassung

* Es gibt drei verschiedene Arten der Stichprobenerstellung: adaptive Stichprobenerstellung, Stichprobenerstellung mit festem Prozentsatz und Erfassungs-Stichprobenerstellung.
* Die adaptive Stichprobenerstellung ist in allen aktuellen Versionen der Application Insights ASP.NET und ASP.NET Core Software Development Kits (SDKs) standardmäßig aktiviert. Sie wird auch von [Azure Functions](../../azure-functions/functions-overview.md) verwendet.
* Die Stichprobenerstellung mit festem Prozentsatz ist in den neuesten Versionen der Application Insights-SDKs für ASP.NET, ASP.NET Core, Java (sowohl Agent als auch SDK) und Python verfügbar.
* Die Erfassungs-Stichprobenerstellung wird auf dem Application Insights-Dienstendpunkt vorgenommen. Sie wird nur angewendet, wenn keine andere Stichprobenerstellung aktiv ist. Wenn das SDK Stichproben Ihrer Telemetriedaten erstellt, ist die Erfassungs-Stichprobenerstellung deaktiviert.
* Wenn Sie im Fall von Webanwendungen benutzerdefinierte Ereignisse protokollieren und dabei sicherstellen müssen, dass eine Gruppe von Ereignissen gemeinsam beibehalten oder verworfen wird, müssen die Ereignisse den gleichen Wert für `OperationId` aufweisen.
* Wenn Sie Analytics-Abfragen schreiben, sollten Sie die [Stichprobenerstellung berücksichtigen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations). Insbesondere sollten Sie nicht einfach nur Datensätze zählen, sondern stattdessen `summarize sum(itemCount)`verwenden.
* Einige Telemetrietypen, einschließlich Leistungsmetriken und benutzerdefinierten Metriken, werden immer beibehalten, unabhängig davon, ob die Stichprobenerstellung aktiviert ist.

In der folgenden Tabelle sind die für die jeweiligen SDKs und Anwendungstypen verfügbaren Arten der Stichprobenerstellung zusammengefasst:

| Application Insights SDK | Adaptive Stichprobenerstellung unterstützt | Stichprobenerstellung mit festem Prozentsatz unterstützt | Erfassungs-Stichprobenerstellung unterstützt |
|-|-|-|-|
| ASP.NET | [Ja (standardmäßig aktiviert)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-applications) | Nur wenn keine andere Stichprobenerstellung aktiv ist |
| ASP.NET Core | [Ja (standardmäßig aktiviert)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Nur wenn keine andere Stichprobenerstellung aktiv ist |
| Azure-Funktionen | [Ja (standardmäßig aktiviert)](#configuring-adaptive-sampling-for-azure-functions) | Nein | Nur wenn keine andere Stichprobenerstellung aktiv ist |
| Java | Nein | [Ja](#configuring-fixed-rate-sampling-for-java-applications) | Nur wenn keine andere Stichprobenerstellung aktiv ist |
| Node.JS | Nein | [Ja](./nodejs.md#sampling) | Nur wenn keine andere Stichprobenerstellung aktiv ist
| Python | Nein | [Ja](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Nur wenn keine andere Stichprobenerstellung aktiv ist |
| Alle anderen | Nein | Nein | [Ja](#ingestion-sampling) |

> [!NOTE]
> Der Großteil der Informationen auf dieser Seite bezieht sich auf die aktuellen Versionen der Application Insights SDKs. Informationen zu älteren Versionen der SDKs [finden Sie im Abschnitt weiter unten](#older-sdk-versions).

## <a name="types-of-sampling"></a>Arten der Stichprobenerstellung

Es gibt drei verschiedene Methoden der Stichprobenerstellung:

* Die **adaptive Stichprobenerstellung** passt die Menge an Telemetriedaten, die vom SDK in Ihrer ASP.NET-/ASP.NET Core-App und von Azure Functions gesendet wird, automatisch an. Dies ist die Standardstichprobenerstellung bei Verwendung des ASP.NET SDK oder ASP.NET Core SDK. Die adaptive Stichprobenerstellung ist derzeit nur für serverseitige Telemetriedaten von ASP.NET und für Azure Functions verfügbar.

* Die **Stichprobenerstellung mit festem Prozentsatz** reduziert die Menge an Telemetriedaten, die von Ihrem ASP.NET-, ASP.NET Core- oder Java-Server und von den Browsern Ihrer Benutzer gesendet wird. Sie legen den Prozentsatz fest. Client und Server synchronisieren ihre Stichprobenerstellung, sodass Sie in der Suche zwischen den verwandten Seitenaufrufen und Anforderungen navigieren können.

* Die **Erfassungs-Stichprobenerstellung** erfolgt auf dem Application Insights-Dienstendpunkt. Bei dieser Methode werden einige Telemetriedaten, die von Ihrer App eingehen, mit der von Ihnen angegebenen Stichprobenerstellungsrate verworfen. Die Methode verringert zwar nicht den von Ihrer App gesendeten Telemetriedatenverkehr, trägt aber zur Einhaltung Ihres monatlichen Kontingents bei. Der Hauptvorteil der Erfassungs-Stichprobenerstellung ist, dass die Stichprobenrate ohne erneute Bereitstellung Ihrer App festgelegt werden kann. Die Erfassungs-Stichprobenerstellung funktioniert für alle Server und Clients gleich, trifft jedoch nicht zu, wenn andere Arten der Stichprobenerstellung ausgeführt werden.

> [!IMPORTANT]
> Während die Methode der adaptiven Stichprobenerstellung oder der Stichprobenerstellung mit festem Prozentsatz für einen Telemetrietyp aktiviert ist, wird die Erfassungs-Stichprobenerstellung für diesen Telemetrietyp deaktiviert. Telemetrietypen, die von der Stichprobenerstellung auf SDK-Ebene ausgeschlossen sind, unterliegen jedoch weiterhin der Erfassungs-Stichprobenerstellung mit dem im Portal festgelegten Satz.

## <a name="adaptive-sampling"></a>Adaptive Stichprobenerstellung

Die adaptive Stichprobenermittlung wirkt sich auf die Menge der Telemetriedaten aus, die von Ihrer Webserver-App an den Application Insights-Dienstendpunkt gesendet werden.

> [!TIP]
> Die adaptive Stichprobenerstellung ist bei Verwendung des ASP.NET SDK oder ASP.NET Core SDK und auch für Azure Functions standardmäßig aktiviert.

Die Menge wird automatisch angepasst, damit sie eine festgelegte maximale Datenverkehrsrate nicht überschreitet, und über die Einstellung `MaxTelemetryItemsPerSecond` gesteuert. Wenn die Anwendung wenig Telemetriedaten erzeugt (z. B. beim Debuggen oder aufgrund geringer Nutzung), werden keine Elemente vom Prozessor für Stichprobenentnahmen gelöscht, solange die Datenmenge unter `MaxTelemetryItemsPerSecond` liegt. Wenn die Menge an Telemetriedaten zunimmt, wird die Stichprobenhäufigkeit angepasst, um die Zielmenge zu erreichen. Die Anpassung wird in regelmäßigen Intervallen neu berechnet und basiert auf dem gleitenden Durchschnitt der ausgehenden Übertragungsrate.

Um die Zielmenge zu erreichen, werden einige der generierten Telemetriedaten verworfen. Aber wie bei den anderen Methoden für die Stichprobenerstellung behält der Algorithmus zugehörige Telemetrieelemente bei. So sind Sie beispielsweise in der Lage, in Search die zu einer bestimmten Ausnahme gehörende Anforderung in den Telemetriedaten zu ermitteln.

Metrikwerte wie z. B. die Anforderungs- und Ausnahmerate werden zum Kompensieren der Stichprobenrate angepasst, sodass im Metrik-Explorer annähernd korrekte Werte angezeigt werden.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurieren der adaptiven Stichprobenerstellung für ASP.NET-Anwendungen

> [!NOTE]
> Dieser Abschnitt gilt für ASP.NET-Anwendungen, jedoch nicht für ASP.NET Core-Anwendungen. [Informationen zum Konfigurieren der adaptiven Stichprobenerstellung für ASP.NET Core-Anwendungen finden Sie weiter unten in diesem Dokument.](#configuring-adaptive-sampling-for-aspnet-core-applications)

In [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) können Sie verschiedene Parameter im `AdaptiveSamplingTelemetryProcessor`-Knoten anpassen. Die folgenden Zahlen veranschaulichen die Standardwerte:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Der Zielprozentsatz von [logischen Vorgängen](./correlation.md#data-model-for-telemetry-correlation), den der Adaptionsalgorithmus **auf jedem einzelnen Serverhost** zu erreichen versucht. Wenn Ihre Web-App auf mehreren Hosts ausgeführt wird, reduzieren Sie diesen Wert, damit Sie innerhalb des Zielprozentsatzes für den Datenverkehr im Application Insights-Portal bleiben.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Das Intervall, in dem die aktuelle Telemetrierate erneut ausgewertet wird. Die Auswertung wird als gleitender Durchschnitt ausgeführt. Wenn bei Ihrer Telemetrie plötzliche Spitzen auftreten, sollten Sie dieses Intervall verkürzen.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Gibt an, nach welchem Zeitraum der Stichproben-Prozentsatz verringert werden darf, um weniger Daten zu erfassen, wenn sich der Wert für den Stichproben-Prozentsatz geändert hat.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Gibt an, nach welchem Zeitraum der Stichproben-Prozentsatz erhöht werden darf, um mehr Daten zu erfassen, wenn sich der Wert für den Stichproben-Prozentsatz geändert hat.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Gibt den minimal zulässigen Wert an, der bei variierendem Stichproben-Prozentsatz festgelegt werden darf.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Gibt den maximal zulässigen Wert an, der bei variierendem Stichproben-Prozentsatz festgelegt werden darf.

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Gibt die Gewichtung an, die bei der Berechnung des gleitenden Durchschnitts dem neuesten Wert zugewiesen werden soll. Verwenden Sie einen Wert kleiner oder gleich 1. Bei einem kleineren Wert reagiert der Algorithmus langsamer auf plötzliche Veränderungen.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Die Menge an Telemetriedaten für die Stichprobenerstellung, wenn die App gerade gestartet wurde. Reduzieren Sie diesen Wert nicht, solange Sie debuggen.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Eine durch Semikolons getrennte Liste von Typen, für die keine Stichproben erstellt werden sollen. Anerkannte Typen sind: `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. Alle Telemetriedaten der angegebenen Typen werden übertragen. Für nicht angegebene Typen werden Stichproben erstellt.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Eine durch Semikolons getrennte Liste von Typen, für die Stichproben erstellt werden sollen. Anerkannte Typen sind: `Dependency`, `Event`, `Exception`, `PageView`, `Request`, `Trace`. Für die angegebenen Typen werden Stichproben erstellt. Alle Telemetriedaten der anderen Typen werden immer übertragen.

Entfernen Sie zum **Deaktivieren** der adaptiven Stichprobenerstellung den bzw. die Knoten `AdaptiveSamplingTelemetryProcessor` aus `ApplicationInsights.config`.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternative: Konfigurieren der adaptiven Stichprobenerstellung im Code

Anstatt den Stichprobenerstellungsparameter in der `.config`-Datei festzulegen, können Sie die Werte auch programmgesteuert festlegen.

1. Entfernen Sie alle Knoten `AdaptiveSamplingTelemetryProcessor` aus der `.config`-Datei.
2. Verwenden Sie den folgenden Codeausschnitt, um die adaptive Stichprobenerstellung zu konfigurieren:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Informieren Sie sich über Telemetrieprozessoren](./api-filtering-sampling.md#filtering).)

Sie können die Stichprobenhäufigkeit auch für jeden Telemetrietyp einzeln anpassen oder sogar bestimmte Typen von der Stichprobenerstellung ausschließen:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurieren der adaptiven Stichprobenerstellung für ASP.NET Core-Anwendungen

Da es für ASP.NET Core-Anwendungen keine Datei `ApplicationInsights.config` gibt, müssen alle Konfigurationen über den Code vorgenommen werden.
Die adaptive Stichprobenerstellung ist standardmäßig für alle ASP.NET Core-Anwendungen aktiviert. Sie können das Verhalten für die Stichprobenentnahme aktivieren oder anpassen.

#### <a name="turning-off-adaptive-sampling"></a>Deaktivieren der adaptiven Stichprobenerstellung

Die standardmäßige Stichprobenerstellung kann beim Hinzufügen des Application Insights-Diensts in der `ConfigureServices`-Methode unter Verwendung von `ApplicationInsightsServiceOptions` in der Datei `Startup.cs` deaktiviert werden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Mit dem Code oben wird die adaptive Stichprobenerstellung deaktiviert. Führen Sie die folgenden Schritte aus, um die Stichprobenentnahme mit weiteren Anpassungsoptionen hinzuzufügen.

#### <a name="configure-sampling-settings"></a>Konfigurieren von Einstellungen für die Stichprobenerstellung

Verwenden Sie die Erweiterungsmethoden von `TelemetryProcessorChainBuilder` wie unten dargestellt, um das Verhalten für die Stichprobenentnahme anzupassen.

> [!IMPORTANT]
> Wenn Sie diese Methode zum Konfigurieren der Stichprobenerstellung verwenden, vergewissern Sie sich, dass die Eigenschaft `aiOptions.EnableAdaptiveSampling` beim Aufrufen von `AddApplicationInsightsTelemetry()` auf `false` festgelegt ist.

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurieren der adaptiven Stichprobenerstellung für Azure Functions

Folgen Sie den Anweisungen auf [dieser Seite](../../azure-functions/configure-monitoring.md#configure-sampling), um die adaptive Stichprobenerstellung für Apps zu konfigurieren, die in Azure Functions ausgeführt werden.

## <a name="fixed-rate-sampling"></a>Stichprobenerstellung mit festem Prozentsatz

Bei der Stichprobenerstellung mit festem Prozentsatz wird der Datenverkehr verringert, der von Ihren Webservern und Webbrowsern gesendet wird. Im Gegensatz zur adaptiven Stichprobenerstellung werden die Telemetriedaten nach einem von Ihnen festgelegten Prozentsatz verringert. Die Stichprobenerstellung mit festem Prozentsatz ist für ASP.NET-, ASP.NET Core-, Java- und Python-Anwendungen verfügbar.

Wie bei anderen Methoden der Stichprobenerstellung werden auch hier verwandte Elemente beibehalten. Darüber hinaus wird die Stichprobenerstellung für Client und Server synchronisiert, sodass zugehörige Elemente beibehalten werden. Wenn Sie also beispielsweise eine Seitenansicht in Search betrachten, können Sie die dazugehörige Serveranforderungen ermitteln. 

Im Metrik-Explorer werden Kennzahlen wie beispielsweise die Anzahl von Anforderungen und Ausnahmen mit einem Faktor multipliziert, um die Stichprobenrate zu kompensieren und annähernd korrekte Werte zu erhalten.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz für ASP.NET-Anwendungen

1. **Deaktivieren der adaptiven Stichprobenerstellung**: Entfernen Sie in [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) den Knoten `AdaptiveSamplingTelemetryProcessor`, oder kommentieren Sie ihn aus.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Aktivieren Sie das Modul für die Stichprobenerstellung mit festem Prozentsatz.** Fügen Sie in [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) den folgenden Codeausschnitt hinzu:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Anstatt den Stichprobenerstellungsparameter in der `ApplicationInsights.config`-Datei festzulegen, können Sie die Werte auch programmgesteuert festlegen:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Informieren Sie sich über Telemetrieprozessoren](./api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz für ASP.NET Core-Anwendungen

1. **Deaktivieren der adaptiven Stichprobenerstellung**:  Änderungen können in der `ConfigureServices`-Methode mit `ApplicationInsightsServiceOptions` vorgenommen werden:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Aktivieren Sie das Modul für die Stichprobenerstellung mit festem Prozentsatz.** Änderungen können wie im folgenden Codeausschnitt gezeigt in der `Configure`-Methode vorgenommen werden:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz für Java-Anwendungen

Standardmäßig ist im Java-Agent und im Java SDK keine Stichprobenerstellung aktiviert. Derzeit wird nur die Stichprobenerstellung mit festem Prozentsatz unterstützt. Adaptive Stichprobenerstellung wird in Java nicht unterstützt.

#### <a name="configuring-java-agent"></a>Konfigurieren des Java-Agents

1. Herunterladen von [applicationinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Fügen Sie der Datei `applicationinsights.json` Folgendes hinzu, um die Stichprobenerstellung zu aktivieren:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>Konfigurieren des Java SDK

1. Laden Sie Ihre Webanwendung mit dem aktuellen [Application Insights Java SDK](./java-get-started.md) herunter, und konfigurieren Sie sie.

2. **Aktivieren Sie das Modul für die Stichprobenerstellung mit festem Prozentsatz** durch Hinzufügen des folgenden Codeausschnitts zur Datei `ApplicationInsights.xml`:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Sie können bestimmte Telemetrietypen in die Stichprobenerstellung einschließen oder davon ausschließen. Dazu verwenden Sie die folgenden Tags innerhalb von `FixedRateSamplingTelemetryProcessor` des `Processor`-Tags:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Folgende Telemetrietypen können in die Stichprobenerstellung eingeschlossen oder davon ausgeschlossen werden: `Dependency`, `Event`, `Exception`, `PageView`, `Request` und `Trace`.

> [!NOTE]
> Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist).  Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz für OpenCensus Python-Anwendungen

Instrumentieren Sie Ihre Anwendung mit der neuesten Version von [OpenCensus Azure Monitor Exporters](./opencensus-python.md).

> [!NOTE]
> Die Stichprobenentnahme mit festem Prozentsatz ist für das Exportprogramm für Metriken nicht verfügbar. Das bedeutet, dass benutzerdefinierte Metriken die einzigen Arten von Telemetriedaten sind, bei denen eine Stichprobenentnahme NICHT konfiguriert werden kann. Das Exportprogramm für Metriken sendet alle Telemetriedaten, die es nachverfolgt.

#### <a name="fixed-rate-sampling-for-tracing"></a>Stichprobenentnahme mit festem Prozentsatz für die Ablaufverfolgung ####
Sie können in Ihrer `Tracer`-Konfiguration ein `sampler`-Element angeben. Wenn kein expliziter Sampler angegeben wird, wird standardmäßig der `ProbabilitySampler` verwendet. Für den `ProbabilitySampler` wird standardmäßig eine Rate von 1/10000 verwendet. Dies bedeutet, dass eine von 10000 Anforderungen an Application Insights gesendet wird. Falls Sie eine Stichprobenhäufigkeit angeben möchten, helfen Ihnen die Informationen unten weiter.

Stellen Sie zum Angeben der Stichprobenhäufigkeit sicher, dass über Ihren `Tracer` ein Sampler mit einer Stichprobenhäufigkeit zwischen 0,0 und 1,0 (einschließlich) angegeben wird. Eine Stichprobenhäufigkeit von 1,0 steht für 100 %. Das bedeutet, dass Ihre gesamten Anforderungen als Telemetriedaten an Application Insights gesendet werden.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Stichprobenentnahme mit festem Prozentsatz für Protokolle ####
Sie können die Stichprobenentnahme mit festem Prozentsatz für `AzureLogHandler` konfigurieren, indem Sie das optionale Argument `logging_sampling_rate` ändern. Wenn kein Argument angegeben wird, wird eine Samplingrate von 1,0 verwendet. Eine Stichprobenhäufigkeit von 1,0 steht für 100 %. Das bedeutet, dass Ihre gesamten Anforderungen als Telemetriedaten an Application Insights gesendet werden.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz für Webseiten mit JavaScript

JavaScript-basierte Webseiten können für die Verwendung von Application Insights konfiguriert werden. Telemetriedaten werden von der Clientanwendung gesendet, die im Browser des Benutzers ausgeführt wird, und die Seiten können von jedem beliebigen Server gehostet werden.

Ändern Sie beim [Konfigurieren Ihrer JavaScript-basierten Webseiten für Application Insights](javascript.md) den JavaScript-Codeausschnitt, den Sie vom Application Insights-Portal erhalten.

> [!TIP]
> Bei ASP.NET-Apps mit enthaltenem JavaScript wird der Codeausschnitt normalerweise in `_Layout.cshtml` eingefügt.

Fügen Sie vor dem Instrumentierungsschlüssel eine Zeile wie `samplingPercentage: 10,` ein:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist). Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Koordinieren der serverseitigen und clientseitigen Stichprobenerstellung

Das clientseitige JavaScript SDK führt die Stichprobenerstellung mit festem Prozentsatz in Verbindung mit dem serverseitigen SDK aus. Die instrumentierten Seiten senden clientseitige Telemetriedaten lediglich von dem Benutzer, für den das serverseitig SDK die Entscheidung zum Einschließen in die Stichprobenerstellung getroffen hat. Mithilfe dieser Logik wird die Integrität der Benutzersitzungen für client- und serverseitige Anwendungen sichergestellt. So können Sie für ein bestimmtes Telemetrieelement in Application Insights nach allen anderen Telemetrieelementen für den jeweiligen Benutzer oder die jeweilige Sitzung suchen. Außerdem können Sie in Search zwischen den verwandten Seitenaufrufen und Anforderungen navigieren.

Falls Ihre client- und serverseitigen Telemetriedaten keine koordinierten Stichproben aufweisen:

* Überprüfen Sie, ob Sie die Stichprobenerstellung sowohl auf dem Server als auch auf dem Client aktiviert haben.
* Stellen Sie sicher, dass Sie auf Client und Server denselben Prozentsatz für die Stichprobenerstellung festgelegt haben.
* Stellen Sie sicher, dass Sie die SDK-Version 2.0 oder höher verwenden.

## <a name="ingestion-sampling"></a>Erfassungs-Stichprobenerstellung

Die Erfassungs-Stichprobenerstellung erfolgt an dem Punkt, an dem die Telemetriedaten von Ihren Webservern, Browsern und Geräten beim Application Insights-Dienstendpunkt eingehen. Diese Methode führt nicht zu einer Verringerung der Telemetriedaten, die von Ihrer App gesendet werden, sie verringert jedoch die Datenmenge, die von Application Insights verarbeitet und beibehalten (und somit in Rechnung gestellt) wird.

Verwenden Sie diese Art der Stichprobenerstellung, wenn Ihre App häufig das monatliche Kontingent überschreitet und Sie keine der SDK-basierten Formen der Stichprobenerstellung verwenden können. 

Legen Sie die Samplingrate auf der Seite „Nutzung und geschätzte Kosten“ fest:

![Klicken Sie auf dem Übersichtsblatt der Anwendung auf „Einstellungen“ > „Kontingent“ > „Stichproben“. Wählen Sie anschließend eine Stichprobenhäufigkeit aus, und klicken Sie auf „Aktualisieren“.](./media/sampling/data-sampling.png)

Wie bei den anderen Methoden für die Stichprobenerstellung behält der Algorithmus zugehörige Telemetrieelemente bei. So sind Sie beispielsweise in der Lage, in Search die zu einer bestimmten Ausnahme gehörende Anforderung in den Telemetriedaten zu ermitteln. Metrikwerte wie z. B. die Anforderungs- und Ausnahmerate werden korrekt beibehalten.

Datenpunkte, die bei der Stichprobenerstellung verworfen werden, stehen in keinem Application Insights-Feature zur Verfügung, auch nicht in [Fortlaufender Export](./export-telemetry.md).

Die Erfassungs-Stichprobenerstellung wird nicht ausgeführt, wenn gleichzeitig eine adaptive Stichprobenerstellung oder eine Stichprobenerstellung mit festem Prozentsatz ausgeführt wird. Die adaptive Stichprobenerstellung ist standardmäßig aktiviert, wenn das ASP.NET SDK oder das ASP.NET Core SDK verwendet wird oder wenn Application Insights in [Azure App Service ](azure-web-apps.md) oder über den Statusmonitor aktiviert ist. Wenn Telemetriedaten vom Application Insights-Dienstendpunkt empfangen werden, werden die Daten untersucht, und wenn eine Stichprobenhäufigkeit kleiner als 100 % gemeldet wird (was darauf hinweist, dass Stichproben der Telemetriedaten erstellt werden), wird die von Ihnen festgelegte Häufigkeit der Erfassungs-Stichprobenerstellung ignoriert.

> [!WARNING]
> Der auf der Portalkachel angezeigte Wert gibt den Wert an, den Sie für die Erfassungs-Stichprobenerstellung festgelegt haben. Er steht nicht für die tatsächliche Stichprobenhäufigkeit, wenn eine beliebige Art von SDK-Stichprobenerstellung (adaptiv oder mit festem Prozentsatz) ausgeführt wird.

## <a name="when-to-use-sampling"></a>Verwendungsbereiche für die Stichprobenerstellung

Für die meisten kleinen und mittelgroßen Anwendungen ist im Allgemeinen keine Stichprobenerstellung erforderlich. Die nützlichsten Diagnoseinformationen und präzisesten Statistiken werden abgerufen, indem Sie Daten zu sämtlichen Benutzeraktivitäten erfassen. 

Die Hauptvorteile von Stichproben sind:

* Der Application Insights-Dienst senkt („drosselt“) Datenpunkte, wenn Ihre App in kurzen Abständen eine große Menge an Telemetriedaten sendet. Durch die Stichprobenerstellung verringert sich die Wahrscheinlichkeit, dass bei Ihrer Anwendung eine Drosselung auftritt.
* Sie können das [Kontingent](pricing.md) an Datenpunkten besser einhalten, das für Ihren Tarif vorgesehen ist. 
* Der Netzwerk-Datenverkehr, der durch die Erfassung von Telemetriedaten verursacht wird, wird reduziert. 

### <a name="which-type-of-sampling-should-i-use"></a>Welche Art der Stichprobenerstellung sollte ich verwenden?

**Verwenden Sie die Erfassungs-Stichprobenerstellung in folgenden Fällen:**

* Sie nutzen häufig Ihr monatliches Kontingent an Telemetriedaten.
* Sie empfangen zu viele Telemetriedaten von den Webbrowsern Ihrer Benutzer.
* Sie verwenden eine SDK-Version, die keine Stichprobenerstellung unterstützt – beispielsweise frühere ASP.NET-Versionen als 2.

**Verwenden Sie die Stichprobenerstellung mit festem Prozentsatz unter folgenden Bedingungen:**

* Sie möchten die Stichprobenerstellung zwischen Client und Server synchronisieren, sodass Sie beim Untersuchen von Ereignissen in [Search](./diagnostic-search.md) zwischen verwandten Ereignissen auf dem Client und dem Server navigieren können, z. B. zwischen Seitenaufrufen und HTTP-Anforderungen.
* Sie sind sich sicher, welcher Stichproben-Prozentsatz für Ihre App angemessen ist. Er sollte hoch genug sein, um genaue Metriken zu erhalten, aber so tief liegen, dass Sie Ihr Tarifkontingent und die Drosselungslimits einhalten.

**Verwendung der adaptiven Stichprobenerstellung:**

Wenn die Bedingungen für die Verwendung der anderen Stichprobenerstellungsarten nicht erfüllt sind, empfiehlt sich die Verwendung der adaptiven Stichprobenerstellung. Diese Einstellung ist im ASP.NET/ASP.NET Core SDK standardmäßig aktiviert. Der Datenverkehr wird erst ab einer bestimmten Mindestrate verringert. Daher werden bei wenig genutzte Websites wahrscheinlich keine Stichproben erstellt.

## <a name="knowing-whether-sampling-is-in-operation"></a>Erkennen, ob die Stichprobenerstellung ausgeführt wird

Verwenden Sie etwa folgende [Analytics-Abfrage](../log-query/log-query-overview.md) , um den tatsächlichen Stichproben-Prozentsatz unabhängig davon zu ermitteln, wo er angewendet wird:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Wenn Sie sehen, dass `RetainedPercentage` für einen beliebigen Typ kleiner als 100 ist, werden für diesen Telemetrietyp Stichproben erstellt.

> [!IMPORTANT]
> Application Insights erstellt bei keiner Methode der Stichprobenerstellung Stichproben für die Telemetrietypen „Sitzung“, „Metriken“ (einschließlich benutzerdefinierter Metriken) und „Leistungsindikatoren“. Diese Telemetrietypen sind immer von der Stichprobenerstellung ausgeschlossen, da eine Reduzierung der Genauigkeit bei ihnen nicht wünschenswert ist.

## <a name="how-sampling-works"></a>Funktionsweise der Stichprobenerstellung

Der Algorithmus für die Stichprobenerstellung entscheidet, welche Telemetrieelemente verworfen und welche beibehalten werden. Dies gilt unabhängig davon, ob die Stichprobenerstellung durch das SDK oder im Application Insights-Dienst erfolgt. Diese Entscheidung im Hinblick auf die Stichprobenerstellung wird basierend auf verschiedenen Regeln getroffen, mit denen sichergestellt wird, dass alle zusammenhängenden Datenpunkte intakt bleiben. Dadurch sind die in Application Insights durchgeführten Diagnosen auch bei einer geringeren Datenmenge zuverlässig und ermöglichen es Ihnen, die erforderlichen Maßnahmen zu ergreifen. Wenn bei Ihrer App beispielsweise eine fehlerhafte Anforderung in einer Stichprobe enthalten ist, werden die zusätzlichen Telemetrieelemente (z. B. Ausnahmen und Ablaufverfolgungen, die für diese Anforderung protokolliert wurden) beibehalten. Bei der Stichprobenerstellung werden entweder alle beibehalten oder alle verworfen. Wenn Sie die Anforderungsdetails in Application Insights anzeigen, wird die Anforderung folglich immer mit den zugehörigen Telemetrieelementen angezeigt.

Die Entscheidung zur Stichprobenerstellung basiert auf der Vorgangs-ID der Anforderung, d. h. alle Telemetrieelemente, die zu einem bestimmten Vorgang gehören, werden entweder beibehalten oder gelöscht. Für Telemetrieelemente, für die keine Vorgangs-ID festgelegt wurde (z. B. Telemetrieelemente aus asynchronen Threads ohne HTTP-Kontext), wird bei der Stichprobenerstellung einfach ein Prozentsatz der Telemetrieelemente jedes Typs erfasst.

Wenn Ihnen die Telemetriedaten angezeigt werden, passt der Application Insights-Dienst die Metriken basierend auf dem Prozentsatz der Stichprobenerstellung an, der bei der Erfassung der Daten verwendet wurde. Auf diese Weise werden die fehlenden Datenpunkte kompensiert. Bei Betrachtung der Telemetriedaten in Application Insights werden folglich statistisch korrekte Annäherungen angezeigt, die den reellen Zahlen sehr nahe kommen.

Die Genauigkeit der Annäherung hängt weitgehend vom konfigurierten Prozentsatz für die Stichprobenerstellung ab. Zudem steigt die Genauigkeit bei Anwendungen, die eine große Anzahl von im Allgemeinen ähnlichen Anforderungen von einer großen Anzahl von Benutzern verarbeiten. Andererseits ist bei Anwendungen mit geringer Last keine Stichprobenerstellung erforderlich, da diese Anwendungen üblicherweise sämtliche Telemetriedaten senden können, ohne dass dabei das jeweilige Kontingent überschritten wird oder es durch die Drosselung zu Datenverlust kommt. 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

*Was ist das Standardverhalten für die Stichprobenerstellung im ASP.NET SDK und ASP.NET Core SDK?*

* Wenn Sie eine der aktuellen Versionen des obigen SDK verwenden, ist die adaptive Stichprobenerstellung standardmäßig mit fünf Telemetrieelementen pro Sekunde aktiviert.
  Es werden standardmäßig zwei `AdaptiveSamplingTelemetryProcessor`-Knoten hinzugefügt, von denen einer den Typ `Event` bei der Stichprobenerstellung einschließt und der andere den Typ `Event` aus der Stichprobenerstellung ausschließt. Bei dieser Konfiguration versucht das SDK, Telemetrieelemente auf fünf Telemetrieelemente vom Typ `Event` und fünf Telemetrieelemente aller anderen Typen (kombiniert) zu begrenzen. Dadurch wird sichergestellt, dass Stichproben für `Events` getrennt von anderen Telemetrietypen erstellt werden. Ereignisse werden normalerweise für Geschäftstelemetriedaten verwendet und sollten in den meisten Fällen nicht durch die Menge an Diagnosetelemetriedaten beeinträchtigt werden.
  
  Nachfolgend wird die generierte Standarddatei `ApplicationInsights.config` gezeigt. In ASP.NET Core wird das gleiche Standardverhalten im Code aktiviert. Verwenden Sie die [Beispiele weiter oben auf dieser Seite](#configuring-adaptive-sampling-for-aspnet-core-applications), um das Standardverhalten zu ändern.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Können mehrmals Stichproben für Telemetrieelemente erstellt werden?*

* Nein. „SamplingTelemetryProcessors“ ignorieren Elemente, wenn bereits eine Stichprobe für sie erstellt wurde. Dies gilt auch für die Erfassungs-Stichprobenerstellung: Elemente, für die bereits im SDK selbst eine Stichprobe erstellt wurde, werden nicht in die Stichprobenerstellung einbezogen.

*Weshalb erfolgt die Stichprobenerstellung nicht einfach nach dem Prinzip „X Prozent jedes Telemetrietyps erfassen“?*

* Diese Methode der Stichprobenerstellung würde bei metrischen Annäherungen eine hohe Genauigkeit bieten, die für Diagnosen entscheidende Korrelation der Diagnosedaten für einzelne Benutzer, Sitzungen und Anforderungen wäre jedoch nicht möglich. Aus diesem Grund sind Richtlinien wie „alle Telemetrieelemente für X Prozent der App-Benutzer erfassen“ oder „alle Telemetriedaten für X Prozent der App-Anforderungen erfassen“ besser für die Stichprobenerstellung geeignet. Für Telemetrieelemente, die nicht mit den Anforderungen verknüpft sind (z. B. die asynchrone Hintergrundverarbeitung), wird auf den Ansatz „X Prozent aller Elemente für jeden Telemetrietyp erfassen“ zurückgegriffen. 

*Kann der Prozentsatz für die Stichprobenerstellung im Verlauf der Zeit geändert werden?*

* Ja, bei der adaptiven Stichprobenerstellung wird der Stichproben-Prozentsatz langsam auf Basis des aktuell beobachteten Telemetrievolumens verändert.

*Wie kann ich bei einer Stichprobenerstellung mit festem Prozentsatz für meine App den idealen Prozentsatz ermitteln?*

* Eine Möglichkeit ist, mit der adaptiven Stichprobenerstellung zu beginnen, herauszufinden, bei welchem Wert sie sich einstellt (siehe die oben gestellt Frage), und dann mit diesem Wert zur Stichprobenerstellung mit festem Prozentsatz zu wechseln. 
  
    Andernfalls müssen Sie raten. Analysieren Sie Ihre aktuelle Nutzung der Telemetriedaten in Application Insights, beobachten Sie ggf. auftretende Drosselungen, und schätzen Sie den Umfang der erfassten Telemetriedaten. In Kombination mit Ihrem ausgewählten Tarif geben diese drei Faktoren Auskunft darüber, um wie viel Sie die Menge der erfassten Telemetriedaten gegebenenfalls reduzieren sollten. Allerdings kann eine Zunahme der Benutzeranzahl oder eine andere Verschiebung in der Menge der Telemetriedaten Ihre Schätzung ungültig werden lassen.

*Was geschieht, wenn ich einen zu geringen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Zu geringe Prozentsätze für die Stichprobenerstellung führen zu einer übermäßige Stichprobenerstellung und wirken sich negativ auf die Genauigkeit der Annäherungen aus, wenn Application Insights versucht, die Visualisierung der Daten für die reduzierte Datenmenge auszugleichen. Auch kann das Diagnoseergebnis beeinträchtigt sein, da einige der langsamen Anforderungen oder Anforderungen, bei denen es selten zu einem Fehler kommt, möglicherweise nicht erfasst werden.

*Was geschieht, wenn ich einen zu hohen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Wenn Sie einen zu hohen Prozentsatz für die Stichprobenerstellung konfigurieren (keine ausreichende Stichprobenerstellung), führt dies zu einer nicht ausreichenden Reduzierung der Menge an erfassten Telemetriedaten. Trotzdem kann es im Zusammenhang mit der Drosselung zum Verlust von Telemetriedaten kommen, und die Kosten für die Nutzung von Application Insights können die geplanten Kosten aufgrund von Überschreitungsgebühren übersteigen.

*Auf welchen Plattformen kann ich die Stichprobenerstellung verwenden?*

* Die Erfassungs-Stichprobenerstellung kann automatisch für alle Telemetriedaten oberhalb eines bestimmten Volumens auftreten, wenn das SDK keine Stichprobenerstellung ausführt. Diese Konfiguration würde z. B. funktionieren, wenn Sie eine ältere Version des ASP.NET SDK oder Java SDK verwenden.
* Bei Verwendung des aktuellen ASP.NET SDK oder ASP.NET Core SDK (gehostet in Azure oder auf Ihrem eigenen Server) erhalten Sie standardmäßig adaptive Stichprobenerstellung, aber Sie können, wie oben beschrieben, zu festem Prozentsatz wechseln. Bei Stichprobenerstellung mit festem Prozentsatz wird das Browser-SDK automatisch mit stichprobenbezogenen Ereignissen synchronisiert. 
* Bei Verwendung des aktuellen Java-Agents können Sie `applicationinsights.json` konfigurieren, um die Stichprobenerstellung mit festem Prozentsatz zu aktivieren. (Für das Java SDK muss `ApplicationInsights.xml` konfiguriert werden.) Stichprobenerstellung ist standardmäßig deaktiviert. Bei Stichprobenerstellung mit festem Prozentsatz werden das Browser-SDK und der Server automatisch mit stichprobenbezogenen Ereignissen synchronisiert.

*Es gibt einige seltene Ereignisse, die ich immer untersuchen möchte. Wie bekomme ich sie durch das Stichprobenmodul?*

* Dies erreichen Sie am besten, indem Sie einen benutzerdefinierten [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) schreiben, der `SamplingPercentage` für das Telemetriedatenelement, das Sie behalten möchten, auf 100 festlegt (wie unten veranschaulicht). Da Initialisierer garantiert vor Telemetrieprozessoren (einschließlich Stichprobenerstellung) ausgeführt werden, ist sichergestellt, dass alle Verfahren zur Stichprobenerstellung dieses Element bei jeglichen Überlegungen der Stichprobenerstellung ignorieren. Benutzerdefinierte Telemetrieinitialisierer sind im ASP.NET SDK, im ASP.NET Core SDK, im JavaScript SDK und im Java SDK verfügbar. Sie können beispielsweise einen Telemetrieinitialisierer mithilfe des ASP.NET SDK konfigurieren:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Ältere SDK-Versionen

Die adaptive Stichprobenerstellung ist für das Application Insights SDK für ASP.NET v2.0.0-beta3 und höher sowie für das Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 und höher verfügbar und standardmäßig aktiviert.

Die Stichprobenerstellung mit festem Prozentsatz ist ein Feature im ASP.NET SDK ab Version 2.0.0 und Java SDK ab Version 2.0.1.

Vor ASP.NET SDK v2.5.0-beta2 und ASP.NET Core SDK v2.2.0-beta3 beruhte die Entscheidung zur Stichprobenerstellung bei Anwendungen, die „Benutzer“ definieren (d. h. den meisten typischen Webanwendungen), auf dem Hash der Benutzer-ID. Bei Anwendungen, die keine Benutzer definieren (z. B. Webdienste), basierte die Entscheidung auf der Vorgangs-ID der Anforderung. Bei aktuellen Versionen des ASP.NET SDK und ASP.NET Core SDK wird die Vorgangs-ID für die Entscheidung bezüglich der Stichprobenerstellung verwendet.

## <a name="next-steps"></a>Nächste Schritte

* [Filtern](./api-filtering-sampling.md) erhalten Sie eine strengere Kontrolle über die Sendungen Ihres SDK.
* Lesen Sie den Developer Network-Artikel [Optimieren von Telemetrie mit Application Insights](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

