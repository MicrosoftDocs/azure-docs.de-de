---
title: Stichprobenerstellung für Telemetriedaten in Azure Application Insights | Microsoft-Dokumentation
description: So behalten Sie die Kontrolle über die Menge an erfassten Telemetriedaten.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: c94167929782a2deca7bba19924bfe67dd46bf29
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388387"
---
# <a name="sampling-in-application-insights"></a>Erstellen von Stichproben in Application Insights

Die Stichprobenerstellung ist eine Funktion in [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Sie ist die empfohlene Methode zum Reduzieren des Telemetriedatenverkehrs und Speicherbedarfs, während gleichzeitig eine statistisch korrekte Analyse der Anwendungsdaten sichergestellt wird. Der Filter wählt verwandte Elemente, sodass Sie beim Ausführen diagnostischer Untersuchungen zwischen Elementen navigieren können.
Bei der Anzeige im Portal werden die Metrikergebnisse zur Berücksichtigung der Stichprobenerstellung renormalisiert. Dadurch werden die Auswirkungen auf die Statistiken minimiert.

Die Stichprobenerstellung reduziert Datenverkehr und Datenkosten und unterstützt Sie dabei, eine Drosselung zu vermeiden.

## <a name="in-brief"></a>Kurz gesagt:

* Bei der Stichprobenerstellung wird ein Datensatz von *n* Datensätzen beibehalten, und der Rest wird verworfen. Beispielsweise kann eines von fünf Ereignissen beibehalten werden, was einer Stichprobenrate von 20 % entspricht. 
* Die adaptive Stichprobenerstellung ist in allen aktuellen Versionen der ASP.NET und ASP.NET Core Software Development Kits (SDKs) standardmäßig aktiviert.
* Sie können die Stichprobenerstellung auch manuell festlegen. Diese Konfiguration kann im Portal auf der *Seite „Nutzung und geschätzte Kosten“* , im ASP.NET SDK in der Datei „ApplicationInsights.config“, im ASP.NET Core SDK über den Code oder im Java SDK in der Datei „ApplicationInsights.xml“ vorgenommen werden.
* Wenn Sie benutzerdefinierte Ereignisse protokollieren und dabei sicherstellen müssen, dass eine Gruppe von Ereignissen gemeinsam beibehalten oder verworfen wird, müssen die Ereignisse den gleichen Wert für „OperationId“ aufweisen.
* Der Stichprobenteiler *n* wird in jedem Datensatz in der Eigenschaft `itemCount` gemeldet, die in der Suche unter dem Anzeigenamen „Anforderungsanzahl“ oder „Ereignisanzahl“ angezeigt wird. `itemCount==1`, wenn keine Stichprobenerstellung aktiv ist.
* Wenn Sie Analytics-Abfragen schreiben, sollten Sie die [Stichprobenerstellung berücksichtigen](../../azure-monitor/log-query/aggregations.md). Insbesondere sollten Sie nicht einfach nur Datensätze zählen, sondern stattdessen `summarize sum(itemCount)`verwenden.

## <a name="types-of-sampling"></a>Arten der Stichprobenerstellung

Es gibt drei alternative Methoden zur Stichprobenerstellung:

* Die **adaptive Stichprobenerstellung** passt die Menge an Telemetriedaten, die vom SDK in Ihrer ASP.NET-/ASP.NET Core-App gesendet wird, automatisch an. Dies ist beim ASP.NET Web SDK ab Version 2.0.0-beta3 und beim Microsoft.ApplicationInsights.AspNetCore SDK ab Version 2.2.0-beta1 die Standardmethode der Stichprobenerstellung.  Die adaptive Stichprobenerstellung ist derzeit nur für serverseitige Telemetriedaten von ASP.NET verfügbar.

* Die **Stichprobenerstellung mit festem Prozentsatz** reduziert die Menge an Telemetriedaten, die von Ihrem ASP.NET-, ASP.NET Core- oder Java-Server und von den Browsern Ihrer Benutzer gesendet wird. Sie legen den Prozentsatz fest. Client und Server synchronisieren ihre Stichprobenerstellung, sodass Sie in der Suche zwischen den verwandten Seitenaufrufen und Anforderungen navigieren können.

* Die **Erfassungs-Stichprobenerstellung** wird im Azure-Portal ausgeführt. Bei dieser Methode werden einige Telemetriedaten, die von Ihrer App eingehen, mit der von Ihnen angegebenen Stichprobenerstellungsrate verworfen. Die Methode verringert zwar nicht den von Ihrer App gesendeten Telemetriedatenverkehr, trägt aber zur Einhaltung Ihres monatlichen Kontingents bei. Der Hauptvorteil der Erfassungs-Stichprobenerstellung ist, dass die Stichprobenrate ohne erneute Bereitstellung Ihrer App festgelegt werden kann. Die Erfassungs-Stichprobenerstellung funktioniert bei allen Servern und Clients gleich.

Während die Stichprobenerstellung mit adaptivem oder festem Prozentsatz aktiv ist, wird die Erfassungs-Stichprobenerstellung deaktiviert.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Adaptive Stichprobenerstellung in Ihren ASP.NET-/ASP.NET Core-Webanwendungen

Die adaptive Stichprobenerstellung ist für das Application Insights SDK für ASP.NET Version 2.0.0-beta3 und höher sowie für das Microsoft.ApplicationInsights.AspNetCore SDK Version 2.2.0-beta1 und höher verfügbar und ist standardmäßig aktiviert.

Die adaptive Stichprobenermittlung wirkt sich auf die Menge der Telemetriedaten aus, die von Ihrer Webserver-App an den Application Insights-Dienstendpunkt gesendet werden. Die Menge wird automatisch angepasst, damit sie eine festgelegte maximale Datenverkehrsrate nicht überschreitet, und über die Einstellung `MaxTelemetryItemsPerSecond` gesteuert. Wenn die Anwendung wenig Telemetriedaten erzeugt (z. B. beim Debuggen oder aufgrund geringer Nutzung), werden keine Stichproben für Elemente erstellt, so lange die Datenmenge unter `MaxTelemetryItemsPerSecond` liegt. Wenn die Menge an Telemetriedaten zunimmt, wird die Stichprobenrate angepasst, um die Zielmenge zu erreichen.

Um die Zielmenge zu erreichen, werden einige der generierten Telemetriedaten verworfen. Aber wie bei den anderen Methoden für die Stichprobenerstellung behält der Algorithmus zugehörige Telemetrieelemente bei. So sind Sie beispielsweise in der Lage, in Search die zu einer bestimmten Ausnahme gehörende Anforderung in den Telemetriedaten zu ermitteln.

Metrikwerte wie z. B. die Anforderungs- und Ausnahmerate werden zum Kompensieren der Stichprobenrate angepasst, sodass im Metrik-Explorer annähernd korrekte Werte angezeigt werden.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurieren der adaptiven Stichprobenerstellung für ASP.NET-Anwendungen

Informationen zum Konfigurieren der adaptiven Stichprobenerstellung für ASP.NET Core-Anwendungen finden Sie [hier](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications). 

In [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) können Sie verschiedene Parameter im `AdaptiveSamplingTelemetryProcessor`-Knoten anpassen. Die folgenden Zahlen veranschaulichen die Standardwerte:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Der Zielprozentsatz, den der Adaptionsalgorithmus **auf jedem einzelnen Serverhost**zu erreichen versucht. Wenn Ihre Web-App auf mehreren Hosts ausgeführt wird, reduzieren Sie diesen Wert, damit Sie innerhalb des Zielprozentsatzes für den Datenverkehr im Application Insights-Portal bleiben.
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
  
    Die Gewichtung, die bei der Berechnung des gleitenden Durchschnitts dem jüngsten Wert beigemessen wird. Verwenden Sie einen Wert kleiner oder gleich 1. Bei einem kleineren Wert reagiert der Algorithmus langsamer auf plötzliche Veränderungen.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Der Wert, der sofort nach dem Start der App zugewiesen wird. Reduzieren Sie den Wert nicht, solange Sie debuggen.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Eine durch Strichpunkte getrennte Liste von Typen, für die keine Stichproben erstellt werden sollen. Anerkannte Typen sind: Dependency, Event, Exception, PageView, Request, Trace. Alle Instanzen der angegebenen Typen werden übertragen. Für nicht angegebene Typen werden Stichproben erstellt.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Eine durch Strichpunkte getrennte Liste von Typen, für die Stichproben erstellt werden sollen. Anerkannte Typen sind: Dependency, Event, Exception, PageView, Request, Trace. Für die angegebenen Typen werden Stichproben erstellt. Alle Instanzen der anderen Typen werden immer übertragen.


Entfernen Sie zum **Deaktivieren** der adaptiven Stichprobenerstellung den bzw. die Knoten „AdaptiveSamplingTelemetryProcessor“ aus „applicationinsights-config“.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternative: Konfigurieren der adaptiven Stichprobenerstellung in Code

Anstatt den Stichprobenerstellungsparameter in der CONFIG-Datei festzulegen, können Sie die Werte auch programmgesteuert festlegen.

1. Entfernen Sie alle Knoten `AdaptiveSamplingTelemetryProcessor` aus der CONFIG-Datei.
2. Verwenden Sie den folgenden Codeausschnitt, um die adaptive Stichprobenerstellung konfigurieren.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Informieren Sie sich über Telemetrieprozessoren](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Sie können die Stichprobenrate auch für jeden Telemetrietyp einzeln anpassen oder sogar bestimmte Typen von der Stichprobenerstellung ausschließen. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurieren der adaptiven Stichprobenerstellung für ASP.NET Core-Anwendungen

Da es für ASP.NET Core-Anwendungen keine Datei `ApplicationInsights.Config` gibt, müssen sämtliche Konfigurationen über den Code vorgenommen werden.
Die adaptive Stichprobenerstellung ist standardmäßig für alle ASP.NET Core-Anwendungen aktiviert. Sie können das Verhalten für die Stichprobenentnahme aktivieren oder anpassen.

### <a name="turning-off-adaptive-sampling"></a>Deaktivieren der adaptiven Stichprobenerstellung

Die standardmäßige Stichprobenerstellung kann beim Hinzufügen des Application Insights-Diensts in der ```ConfigureServices```-Methode unter Verwendung von ```ApplicationInsightsServiceOptions``` in der Datei `Startup.cs` deaktiviert werden:

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

Mit dem Code oben wird die Stichprobenentnahme deaktiviert. Führen Sie die folgenden Schritte aus, um die Stichprobenentnahme mit weiteren Anpassungsoptionen hinzuzufügen.

### <a name="configure-sampling-settings"></a>Konfigurieren von Einstellungen für die Stichprobenentnahme

Verwenden Sie die Erweiterungsmethoden von ```TelemetryProcessorChainBuilder``` wie unten dargestellt, um das Verhalten für die Stichprobenentnahme anzupassen.

> [!IMPORTANT]
> Wenn Sie diese Methode zum Konfigurieren der Stichprobenerstellung verwenden, müssen Sie die Einstellungen „aiOptions.EnableAdaptiveSampling = false;“ mit „AddApplicationInsightsTelemetry()“ verwenden.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Bei Verwendung der obigen Methode zum Konfigurieren der Stichprobenerstellung müssen Sie ```aiOptions.EnableAdaptiveSampling = false;```-Einstellungen mit „AddApplicationInsightsTelemetry()“ verwenden.**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Stichprobenerstellung mit festem Prozentsatz für ASP.NET-, ASP.NET Core- und Java-Websites

Bei der Stichprobenerstellung mit festem Prozentsatz wird der Datenverkehr verringert, der von Ihren Webservern und Webbrowsern gesendet wird. Im Gegensatz zur adaptiven Stichprobenerstellung werden die Telemetriedaten nach einem von Ihnen festgelegten Prozentsatz verringert. Darüber hinaus wird die Stichprobenerstellung für Client und Server synchronisiert, sodass zugehörige Elemente beibehalten werden. Wenn Sie also beispielsweise eine Seitenansicht in Search betrachten, können Sie die dazugehörige Anforderung ermitteln.

Wie bei anderen Methoden der Stichprobenerstellung werden auch hier verwandte Elemente beibehalten. Für jedes HTTP-Anforderungsereignis werden die Anforderung selbst und die dazugehörigen Ereignisse entweder gemeinsam verworfen oder übermittelt.

Im Metrik-Explorer werden Kennzahlen wie beispielsweise die Anzahl von Anforderungen und Ausnahmen mit einem Faktor multipliziert, um die Stichprobenrate zu kompensieren und annähernd korrekte Werte zu erhalten.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz in ASP.NET

1. **Deaktivieren der adaptiven Stichprobenerstellung**: Entfernen Sie in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) den `AdaptiveSamplingTelemetryProcessor`-Knoten, oder kommentieren Sie ihn aus.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Aktivieren Sie das Modul für die Stichprobenerstellung mit festem Prozentsatz.** Fügen Sie in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)diesen Codeausschnitt hinzu:
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternative: Aktivieren der Stichprobenerstellung mit festem Prozentsatz in Ihrem Servercode
    
    Anstatt den Stichprobenerstellungsparameter in der CONFIG-Datei festzulegen, können Sie die Werte auch programmgesteuert festlegen. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Informieren Sie sich über Telemetrieprozessoren](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz in ASP.NET Core

1. **Deaktivieren der adaptiven Stichprobenerstellung**:  Änderungen können in der ```ConfigureServices```-Methode mit ```ApplicationInsightsServiceOptions``` vorgenommen werden:

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

2. **Aktivieren Sie das Modul für die Stichprobenerstellung mit festem Prozentsatz.** Änderungen können wie im folgenden Codeausschnitt gezeigt in der ```Configure```-Methode vorgenommen werden:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurieren der Stichprobenerstellung mit festem Prozentsatz in Java ###

1. Herunterladen und Konfigurieren Ihrer Webanwendung mit dem aktuellen [Application Insights-Java-SDK](../../azure-monitor/app/java-get-started.md)

2. **Aktivieren Sie das Modul für die Stichprobenerstellung mit festem Prozentsatz** durch Hinzufügen des folgenden Codeausschnitts zur ApplicationInsights.xml-Datei.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. Sie können bestimmte Telemetriedatentypen mithilfe der folgenden Tags innerhalb des Processor-Tags FixedRateSamplingTelemetryProcessor in die Stichprobenerstellung einschließen oder von ihr ausschließen.
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Folgende Telemetrietypen können in die Stichprobenerstellung eingeschlossen oder davon ausgeschlossen werden: „Dependency“, „Event“, „Exception“, „PageView“, „Request“ und „Trace“.

> [!NOTE]
> Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist).  Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>Erfassungs-Stichprobenerstellung

Diese Form der Stichprobenerstellung arbeitet an dem Punkt, an dem die Telemetriedaten von Ihren Webservern, Browser und Geräten beim Application Insights-Dienstendpunkt eingehen. Diese Methode führt nicht zu einer Verringerung der Telemetriedaten, die von Ihrer App gesendet werden, sie verringert jedoch die Datenmenge, die von Application Insights verarbeitet und beibehalten (und somit in Rechnung gestellt) wird.

Verwenden Sie diese Art der Stichprobenerstellung, wenn Ihre App häufig das monatliche Kontingent überschreitet und Sie keine der SDK-basierten Formen der Stichprobenerstellung verwenden können. 

Legen Sie die Samplingrate auf der Seite „Nutzung und geschätzte Kosten“ fest:

![Klicken Sie auf dem Blatt zur Anwendungsübersicht auf „Einstellungen“ > „Kontingent“ > „Stichproben“. Wählen Sie anschließend eine Stichprobenrate aus, und klicken Sie auf „Aktualisieren“.](./media/sampling/04.png)

Wie bei den anderen Methoden für die Stichprobenerstellung behält der Algorithmus zugehörige Telemetrieelemente bei. So sind Sie beispielsweise in der Lage, in Search die zu einer bestimmten Ausnahme gehörende Anforderung in den Telemetriedaten zu ermitteln. Metrikwerte wie z. B. die Anforderungs- und Ausnahmerate werden korrekt beibehalten.

Datenpunkte, die bei der Stichprobenerstellung verworfen werden, stehen in keinem Application Insights-Feature zur Verfügung, auch nicht in [Fortlaufender Export](../../azure-monitor/app/export-telemetry.md).

Die Erfassungs-Stichprobenerstellung wird nicht ausgeführt, wenn gleichzeitig eine SDK-basierte Stichprobenerstellung mit adaptivem oder festem Prozentsatz ausgeführt wird. Die adaptive Stichprobenerstellung ist standardmäßig aktiviert, wenn das ASP.NET/ASP.NET Core SDK in Visual Studio aktiviert ist oder in Azure-Web-App-Erweiterungen oder mithilfe des Statusmonitors aktiviert wird und die Erfassungs-Stichprobenerstellung deaktiviert ist. Wenn die Stichprobenrate im SDK unter 100 % liegt (d. h. für Elemente werden Stichproben erstellt), wird die von Ihnen festgelegte Rate für die Erfassungs-Stichprobenerstellung ignoriert.

> [!WARNING]
> Der auf der Kachel angezeigte Wert gibt den Wert an, den Sie für die Erfassungs-Stichprobenerstellung festgelegt haben. Er steht nicht für den tatsächlichen Stichprobensatz, wenn die SDK-Stichprobenerstellung ausgeführt wird.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Erstellen von Stichproben für Webseiten mit JavaScript
Sie können einen beliebigen Server verwenden, um Webseiten für die Erstellung von Stichproben mit festem Prozentsatz zu konfigurieren. 

Ändern Sie beim [Konfigurieren der Webseiten für Application Insights](../../azure-monitor/app/javascript.md) den JavaScript-Codeausschnitt aus dem Application Insights-Portal. (In ASP.NET-Apps wird der Codeausschnitt normalerweise in _Layout.cshtml eingefügt.)  Fügen Sie vor dem Instrumentierungsschlüssel eine Zeile wie `samplingPercentage: 10,` ein:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist).  Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.

Wenn Sie auch auf dem Server die Stichprobenerstellung mit festem Prozentsatz aktivieren, wird die Stichprobenerstellung zwischen Client und Server synchronisiert, sodass Sie in Search zwischen den verwandten Seitenaufrufen und Anforderungen navigieren können.

## <a name="when-to-use-sampling"></a>Wann sollten Sie die Stichprobenerstellung nutzen?

Die adaptive Stichprobenerstellung ist in den aktuellen .NET und .NET Core SDKs automatisch aktiviert. Unabhängig von der verwendeten SDK-Version können Sie die Erfassungs-Stichprobenerstellung aktivieren, um Application Insights die Stichprobenerstellung für die gesammelten Daten zu ermöglichen.

Standardmäßig ist keine Stichprobenerstellung im Java-SDK aktiviert. Derzeit wird nur Stichprobenerstellung mit festem Prozentsatz unterstützt. Adaptive Stichprobenerstellung wird im Java-SDK nicht unterstützt.

Für die meisten kleinen und mittelgroßen Anwendungen ist im Allgemeinen keine Stichprobenerstellung erforderlich. Die nützlichsten Diagnoseinformationen und präzisesten Statistiken werden abgerufen, indem Sie Daten zu sämtlichen Benutzeraktivitäten erfassen. 

Die Hauptvorteile von Stichproben sind:

* Der Application Insights-Dienst senkt („drosselt“) Datenpunkte, wenn Ihre App in kurzen Abständen eine große Menge an Telemetriedaten sendet. 
* Sie können das [Kontingent](../../azure-monitor/app/pricing.md) an Datenpunkten besser einhalten, das für Ihren Tarif vorgesehen ist. 
* Der Netzwerk-Datenverkehr, der durch die Erfassung von Telemetriedaten verursacht wird, wird reduziert. 

### <a name="which-type-of-sampling-should-i-use"></a>Welche Art der Stichprobenerstellung sollte ich verwenden?

**Verwenden Sie die Erfassungs-Stichprobenerstellung in folgenden Fällen:**

* Sie überschreiten häufig Ihr monatlichen Kontingents für Telemetriedaten.
* Sie verwenden eine SDK-Version, die keine Stichprobenerstellung unterstützt – beispielsweise frühere ASP.NET-Versionen als 2.
* Sie empfangen zu viele Telemetriedaten von den Webbrowsern Ihrer Benutzer.

**Verwenden Sie die Stichprobenerstellung mit festem Prozentsatz unter folgenden Bedingungen:**

* Sie verwenden das Application Insights-SDK für ASP.NET-Webdienste Version 2.0.0 oder höher bzw. Java-SDK v2.0.1 oder höher, und
* Sie möchten die Stichprobenerstellung zwischen Client und Server synchronisieren, sodass Sie beim Untersuchen von Ereignissen in der [Suche](../../azure-monitor/app/diagnostic-search.md) zwischen verwandten Ereignissen auf dem Client und dem Server navigieren können, z.B. zwischen Seitenaufrufen und HTTP-Anforderungen.
* Sie sind sich sicher, welcher Stichproben-Prozentsatz für Ihre App angemessen ist. Er sollte hoch genug sein, um genaue Metriken zu erhalten, aber so tief liegen, dass Sie Ihr Tarifkontingent und die Drosselungslimits einhalten. 

**Verwendung der adaptiven Stichprobenerstellung:**

Wenn die Bedingungen für die Verwendung der anderen Stichprobenerstellungsarten nicht erfüllt sind, empfiehlt sich die Verwendung der adaptiven Stichprobenerstellung. Diese Einstellung ist im ASP.NET/ASP.NET Core Server SDK standardmäßig aktiviert. Der Datenverkehr wird erst ab einer bestimmten Mindestrate verringert. Wenig genutzte Websites sind daher nicht betroffen.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Woher weiß ich, dass die Stichprobenerfassung ausgeführt wird?

Verwenden Sie etwa folgende [Analytics-Abfrage](../../azure-monitor/app/analytics.md) , um den tatsächlichen Stichproben-Prozentsatz unabhängig davon zu ermitteln, wo er angewendet wird:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Wenn der Wert von „RetainedPercentage“ für einen Typ kleiner als 100 ist, werden Stichproben für das jeweilige Element erstellt.

**Application Insights erstellt bei keiner der oben beschriebenen Methoden der Stichprobenerstellung Stichproben für die Telemetrietypen „Sitzung“, „Metrik“ und „Leistungsindikatoren“. Diese Typen sind immer von der Stichprobenerstellung ausgeschlossen, da eine Reduzierung der Genauigkeit bei ihnen nicht wünschenswert ist.**

## <a name="how-does-sampling-work"></a>Wie funktioniert die Stichprobenerstellung?

Feature Stichprobenerstellung mit festem Prozentsatz im ASP.NET-SDK ab Version 2.0.0 und Java-SDK ab Version 2.0.1. Adaptive Stichprobenerstellung ist ein Feature des SDK in ASP.NET, Version 2.0.0 oder höher. Die Erfassungs-Stichprobenerstellung ist ein Feature des Application Insights-Diensts und kann ausgeführt werden, wenn das SDK keine Stichprobenerstellung ausführt.

Der Stichprobenerstellungs-Algorithmus entscheidet, welche Telemetriedaten verworfen, und welche beibehalten werden (sowohl im SDK als auch im Application Insights-Dienst). Diese Entscheidung im Hinblick auf die Stichprobenerstellung wird basierend auf verschiedenen Regeln getroffen, mit denen sichergestellt wird, dass alle zusammenhängenden Datenpunkte intakt bleiben. Dadurch sind die in Application Insights durchgeführten Diagnosen auch bei einer geringeren Datenmenge zuverlässig und ermöglichen es Ihnen, die erforderlichen Maßnahmen zu ergreifen. Wenn Ihre App beispielsweise zusätzliche Telemetrieelemente für eine Anforderung sendet, bei der ein Fehler aufgetreten ist (z. B. Ausnahmen und Ablaufverfolgungen, die für diese Anforderung protokolliert wurden), werden diese Anforderung und andere Telemetriedaten bei der Stichprobenerstellung nicht getrennt. Die Daten werden entweder vollständig beibehalten oder vollständig verworfen. Wenn Sie die Anforderungsdetails in Application Insights anzeigen, wird die Anforderung folglich immer mit den zugehörigen Telemetrieelementen angezeigt. 

Die Entscheidung zur Stichprobenerstellung basiert auf der Vorgangs-ID der Anforderung, d. h. alle Telemetrieelemente, die zu einem bestimmten Vorgang gehören, werden entweder beibehalten oder gelöscht. Für Telemetrieelemente, für die keine Vorgangs-ID festgelegt wurde (z. B. Telemetrieelemente aus asynchronen Threads ohne HTTP-Kontext), wird bei der Stichprobenerstellung ein Prozentsatz der Telemetrieelemente jedes Typs erfasst. Vor .NET SDK-Version 2.5.0-beta2 und ASP.NET Core SDK-Version 2.2.0-beta3 beruhte die Entscheidung zur Stichprobenerstellung bei Anwendungen, die „Benutzer“ definieren (d. h. den meisten typischen Webanwendungen), auf dem Hash der Benutzer-ID. Bei Anwendungen, die keine Benutzer definieren (z. B. Webdienste), basierte die Entscheidung auf der Vorgangs-ID der Anforderung.

Wenn Ihnen die Telemetriedaten angezeigt werden, passt der Application Insights-Dienst die Metriken basierend auf dem Prozentsatz der Stichprobenerstellung an, der bei der Erfassung der Daten verwendet wurde. Auf diese Weise werden die fehlenden Datenpunkte kompensiert. Bei Betrachtung der Telemetriedaten in Application Insights werden folglich statistisch korrekte Annäherungen angezeigt, die den reellen Zahlen sehr nahe kommen.

Die Genauigkeit der Annäherung hängt weitgehend vom konfigurierten Prozentsatz für die Stichprobenerstellung ab. Zudem steigt die Genauigkeit bei Anwendungen, die eine große Anzahl von im Allgemeinen ähnlichen Anforderungen von einer großen Anzahl von Benutzern verarbeiten. Andererseits ist bei Anwendungen mit geringer Last keine Stichprobenerstellung erforderlich, da diese Anwendungen üblicherweise sämtliche Telemetriedaten senden können, ohne dass dabei das jeweilige Kontingent überschritten wird oder es durch die Drosselung zu Datenverlust kommt. 

> [!WARNING]
> Application Insights erstellt keine Stichproben für die Telemetrietypen „Metriken“ und „Sitzungen“. Bei diesen Typen ist eine Reduzierung der Genauigkeit nicht wünschenswert.
> 

### <a name="adaptive-sampling"></a>Adaptive Stichprobenerstellung

Bei der adaptiven Stichprobenerstellung wird eine Komponente hinzugefügt, die die aktuelle Übertragungsrate vom SDK überwacht und den Stichproben-Prozentsatz anpasst, um den angestrebten maximalen Prozentsatz einzuhalten. Die Anpassung wird in regelmäßigen Intervallen neu berechnet und basiert auf dem gleitenden Durchschnitt der ausgehenden Übertragungsrate.

## <a name="sampling-and-the-javascript-sdk"></a>Stichprobenerstellung und das JavaScript SDK

Das clientseitige (JavaScript) SDK führt die Stichprobenerstellung mit festem Prozentsatz in Verbindung mit dem serverseitigen SDK aus. Die instrumentierten Seiten senden clientseitige Telemetriedaten lediglich von den Benutzern, für die serverseitig die Entscheidung zur Stichprobenerstellung getroffen wurde. Mithilfe dieser Logik wird die Integrität der Benutzersitzung auf Client- und Serverseite sichergestellt. So können Sie für ein bestimmtes Telemetrieelement in Application Insights nach allen anderen Telemetrieelementen für den jeweiligen Benutzer oder die jeweilige Sitzung suchen. 

*Meine client- und serverseitigen Telemetriedaten weisen keine koordinierten Stichproben auf.*

* Überprüfen Sie, ob Sie die Stichprobenerstellung mit festem Prozentsatz sowohl auf dem Server als auch auf dem Client aktiviert haben.
* Stellen Sie sicher, dass Sie die SDK-Version 2.0 oder höher verwenden.
* Stellen Sie sicher, dass Sie auf Client und Server denselben Prozentsatz für die Stichprobenerstellung festgelegt haben.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

*Was ist das Standardverhalten für die Stichprobenerstellung im ASP.NET und ASP.NET Core SDK?*

* Wenn Sie eine der aktuellen Versionen des obigen SDK verwenden, ist die adaptive Stichprobenerstellung standardmäßig mit fünf Telemetrieelementen pro Sekunde aktiviert.
  Standardmäßig werden zwei Knoten „AdaptiveSamplingTelemetryProcessor“ hinzugefügt. Ein Knoten schließt den Typ „Event“ in die Stichprobenerstellung ein, und der andere Knoten schließt den Typ „Event“ von der Stichprobenerstellung aus. Bei dieser Konfiguration versucht das SDK, Telemetrieelemente auf fünf Telemetrieelemente vom Typ „Event“ und fünf Telemetrieelemente aller anderen Typen (kombiniert) zu begrenzen. Dadurch wird sichergestellt, dass Stichproben für Ereignisse getrennt von anderen Telemetrietypen erstellt werden. Ereignisse werden normalerweise für Geschäftstelemetriedaten verwendet und sollten in den meisten Fällen nicht durch die Menge an Diagnosetelemetriedaten beeinträchtigt werden.
  
  Das folgende Beispiel zeigt die generierte Standarddatei „ApplicationInsights.config“. Wie bereits erwähnt, werden zwei separate Knoten „AdaptiveSamplingTelemetryProcessor“ hinzugefügt: ein Knoten, der den Typ „Event“ ausschließt, und ein weiterer Knoten, der diesen Typ einschließt. In ASP.NET Core wird genau das gleiche Standardverhalten im Code aktiviert. Verwenden Sie die Beispiele weiter oben in diesem Dokument, um das Standardverhalten zu ändern.

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

* Diese Methode der Stichprobenerstellung würde bei metrischen Annäherungen eine hohe Genauigkeit bieten, die für Diagnosen entscheidende Korrelation der Diagnosedaten für einzelne Benutzer, Sitzungen und Anforderungen wäre jedoch nicht möglich. Aus diesem Grund sind „alle Telemetrieelemente für X Prozent der App-Benutzer“ oder „alle Telemetriedaten für X Prozent der App-Anforderungen“ besser geeignete Ansätze an die Stichprobenerstellung. Für Telemetrieelemente, die nicht mit den Anforderungen verknüpft sind (z. B. die asynchrone Hintergrundverarbeitung), wird auf den Ansatz „X Prozent aller Elemente für jeden Telemetrietyp erfassen“ zurückgegriffen. 

*Kann der Prozentsatz für die Stichprobenerstellung im Verlauf der Zeit geändert werden?*

* Ja, bei der adaptiven Stichprobenerstellung wird der Stichproben-Prozentsatz langsam auf Basis des aktuell beobachteten Telemetrievolumens verändert.

*Wie kann ich bei einer Stichprobenerstellung mit festem Prozentsatz für meine App den idealen Prozentsatz ermitteln?*

* Eine Möglichkeit ist, mit der adaptiven Stichprobenerstellung zu beginnen, herauszufinden, bei welchem Wert sie sich einstellt (siehe die oben gestellt Frage), und dann mit diesem Wert zur Stichprobenerstellung mit festem Prozentsatz zu wechseln. 
  
    Andernfalls müssen Sie raten. Analysieren Sie Ihre aktuelle Nutzung der Telemetriedaten in Application Insights, beobachten Sie ggf. auftretende Drosselungen, und schätzen Sie den Umfang der erfassten Telemetriedaten. In Kombination mit Ihrem ausgewählten Tarif geben diese drei Faktoren Auskunft darüber, um wie viel Sie die Menge der erfassten Telemetriedaten gegebenenfalls reduzieren sollten. Allerdings kann eine Zunahme der Benutzeranzahl oder eine andere Verschiebung in der Menge der Telemetriedaten Ihre Schätzung ungültig werden lassen.

*Was geschieht, wenn ich einen zu geringen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Ein deutlich zu geringer Prozentsatz für die Stichprobenerstellung (übermäßige Stichprobenerstellung) wirkt sich negativ auf die Genauigkeit der Annäherungen aus, wenn Application Insights versucht, die Visualisierung der Daten für die reduzierte Datenmenge auszugleichen. Auch kann das Diagnoseergebnis beeinträchtigt sein, da einige der langsamen Anforderungen oder Anforderungen, bei denen es selten zu einem Fehler kommt, möglicherweise nicht erfasst werden.

*Was geschieht, wenn ich einen zu hohen Prozentsatz für die Stichprobenerstellung konfiguriere?*

* Wenn Sie einen zu hohen Prozentsatz für die Stichprobenerstellung konfigurieren (keine ausreichende Stichprobenerstellung), führt dies zu einer nicht ausreichenden Reduzierung der Menge an erfassten Telemetriedaten. Trotzdem kann es im Zusammenhang mit der Drosselung zum Verlust von Telemetriedaten kommen, und die Kosten für die Nutzung von Application Insights können die geplanten Kosten aufgrund von Überschreitungsgebühren übersteigen.

*Auf welchen Plattformen kann ich die Stichprobenerstellung verwenden?*

* Die Erfassungs-Stichprobenerstellung kann automatisch für alle Telemetriedaten oberhalb eines bestimmten Volumens auftreten, wenn das SDK keine Stichprobenerstellung ausführt. Diese Konfiguration würde z. B. funktionieren, wenn Sie eine ältere Version des ASP.NET SDK oder eine frühere Version des Java SDK (1.0.10 oder früher) verwenden.
* Bei der Verwendung von ASP.NET SDK-Version 2.0.0 und höher oder ASP.NET Core SDK-Version 2.2.0 und höher (gehostet in Azure oder auf Ihrem eigenen Server) wird standardmäßig die adaptive Stichprobenerstellung verwendet. Sie können aber wie oben beschrieben zur Stichprobenerstellung mit festem Prozentsatz wechseln. Bei Stichprobenerstellung mit festem Prozentsatz wird das Browser-SDK automatisch mit stichprobenbezogenen Ereignissen synchronisiert. 
* Wenn Sie Java SDK-Version 2.0.1 oder höher verwenden, können Sie „ApplicationInsights.xml“ konfigurieren, um die Stichprobenerstellung mit festem Prozentsatz zu aktivieren. Stichprobenerstellung ist standardmäßig deaktiviert. Bei Stichprobenerstellung mit festem Prozentsatz wird das Browser-SDK automatisch mit stichprobenbezogenen Ereignissen synchronisiert.

*Es gibt einige seltene Ereignisse, die ich immer untersuchen möchte. Wie bekomme ich sie durch das Stichprobenmodul?*

* Dies erreichen Sie am besten, indem Sie einen benutzerdefinierten [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering) schreiben, der `SamplingPercentage` für das Telemetriedatenelement, das Sie behalten möchten, auf 100 festlegt (wie unten veranschaulicht). Dadurch wird sichergestellt, dass alle Verfahren zur Stichprobenerstellung dieses Element in allen Überlegungen der Stichprobenerstellung ignorieren.

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>Nächste Schritte

* [Filtern](../../azure-monitor/app/api-filtering-sampling.md) erhalten Sie eine strengere Kontrolle über die Sendungen Ihres SDK.
* Lesen Sie den Developer Network-Artikel [Optimieren von Telemetrie mit Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
