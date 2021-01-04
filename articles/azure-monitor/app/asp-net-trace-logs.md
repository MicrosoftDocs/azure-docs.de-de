---
title: Untersuchen von .NET-Ablaufverfolgungsprotokollen in Application Insights
description: Suchen Sie nach mit Trace, NLog oder Log4Net generierten Protokollen.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 90777da4d0b67587afebaa7111e3503af2afcb9a
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920350"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Untersuchen von .NET/.NET Core- und Python-Ablaufverfolgungsprotokollen in Application Insights

Senden Sie Protokolle für die Diagnoseablaufverfolgung für Ihre ASP.NET/ASP.NET Core-Anwendung von ILogger, NLog, log4Net oder System.Diagnostics.Trace an [Azure Application Insights][start]. Für Python-Anwendungen senden Sie Protokolle für die Diagnoseablaufverfolgung mithilfe von AzureLogHandler in OpenCensus Python für Azure Monitor. Sie können Sie anschließend untersuchen und nach ihnen suchen. Diese Protokolle werden mit den anderen Protokolldateien Ihrer Anwendung zusammengeführt, sodass Sie Ablaufverfolgungen identifizieren können, die jeder Benutzeranforderung zugeordnet sind, und sie mit anderen Ereignissen und Ausnahmeberichten in Beziehung setzen können.

> [!NOTE]
> Benötigen ich das Protokollerfassungsmodul? Dabei handelt es sich um einen nützlichen Adapter für die Protokollierung von Drittanbietern. Falls Sie nicht bereits NLog, log4Net oder „System.Diagnostics.Trace“ verwenden, können Sie auch einfach direkt [**Application Insights TrackTrace()**](./api-custom-events-metrics.md#tracktrace) aufrufen.
>
>
## <a name="install-logging-on-your-app"></a>Installieren der Protokollierung in Ihrer App
Installieren Sie das von Ihnen gewählte Protokollierungsframework in Ihrem Projekt, was zu einem Eintrag in „app.config“ oder „web.config“ führen sollte.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurieren von Application Insights für das Erfassen von Protokollen
Falls Sie dies noch nicht getan haben, [fügen Sie Ihrem Projekt Application Insights](./asp-net.md) hinzu. Es wird eine Option angezeigt, mit der Sie den Log Collector einfügen können.

Sie können auch im Projektmappen-Explorer mit der rechten Maustaste auf **Application Insights konfigurieren** klicken. Wählen Sie die Option **Ablaufverfolgungssammlung konfigurieren**.

> [!NOTE]
> Kein Application Insights-Menü und keine Log Collector-Option? Lesen Sie in der [Problembehandlung](#troubleshooting)nach.

## <a name="manual-installation"></a>Manuelle Installation
Verwenden Sie diese Methode, wenn der Projekttyp vom Application Insights-Installationsprogramm nicht unterstützt wird (z. B. bei einem Windows-Desktopprojekt).

1. Wenn Sie log4Net oder NLog verwenden möchten, installieren Sie es in Ihrem Projekt.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.
3. Suchen Sie nach „Application Insights“.
4. Wählen Sie eins der folgenden Pakete aus:

   - Für ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet iLogger-Banner](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Für NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet NLog-Banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Für Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet Log4Net-Banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Für System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet System.Diagnostics-Banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet Diagnostic Source Listener-Banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet Etw Collector-Banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![NuGet Event Source Listener-Banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Das NuGet-Paket installiert die erforderlichen Assemblys und ändert ggf. die Datei „web.config“ oder „app.config“.

## <a name="ilogger"></a>ILogger

Beispiele für die Verwendung der Application Insights-ILogger-Implementierung mit Konsolenanwendungen und ASP.NET Core finden Sie i Artikel [ApplicationInsightsLoggerProvider für .NET Core-ILogger-Protokolle](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Einfügen von Diagnoseprotokollaufrufen
Wenn Sie System.Diagnostics.Trace verwenden, wäre ein typischer Aufruf:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Wenn Sie log4net oder NLog bevorzugen, verwenden Sie:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>Verwenden von EventSource-Ereignissen
Sie können [System.Diagnostics.Tracing.EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1)-Ereignisse so konfigurieren, dass Sie als Ablaufverfolgungen an Application Insights gesendet werden. Installieren Sie zunächst das `Microsoft.ApplicationInsights.EventSourceListener`-NuGet-Paket. Bearbeiten Sie anschließend den Abschnitt `TelemetryModules` der Datei [ApplicationInsights.config](./configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Für jede Datenquelle können Sie die folgenden Parameter festlegen:
 * **Name** gibt den Namen des zu erfassenden EventSource-Elements an.
 * **Ebene** gibt den zu erfassenden Protokolliergrad an: *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose* oder *Warning*.
 * **Schlüsselwörter** (Optional) Gibt den ganzzahligen Wert der zu verwendenden Schlüsselwortkombinationen an.

## <a name="use-diagnosticsource-events"></a>Verwenden von DiagnosticSource-Ereignissen
Sie können [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)-Ereignisse so konfigurieren, dass sie als Ablaufverfolgungen an Application Insights gesendet werden. Installieren Sie zunächst das NuGet-Paket [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Bearbeiten Sie anschließend den Abschnitt „TelemetryModules“ der Datei [ApplicationInsights.config](./configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Fügen Sie für jedes DiagnosticSource-Ereignis, das nachverfolgt werden soll, einen Eintrag hinzu, bei dem das Attribut **Name** auf den Namen von DiagnosticSource festgelegt ist.

## <a name="use-etw-events"></a>Verwenden von ETW-Ereignissen
Sie können ETW-Ereignisse (Ereignisablaufverfolgung für Windows) so konfigurieren, dass sie als Ablaufverfolgungen an Application Insights gesendet werden. Installieren Sie zunächst das `Microsoft.ApplicationInsights.EtwCollector`-NuGet-Paket. Bearbeiten Sie anschließend den Abschnitt „TelemetryModules“ der Datei [ApplicationInsights.config](./configuration-with-applicationinsights-config.md).

> [!NOTE] 
> ETW-Ereignisse können nur gesammelt werden, wenn der Prozess, der das SDK hostet, unter einer Identität ausgeführt wird, die Mitglied von „Leistungsprotokollbenutzer“ oder „Administratoren“ ist.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Für jede Datenquelle können Sie die folgenden Parameter festlegen:
 * **ProviderName** ist der Name des zu erfassenden ETW-Anbieters.
 * **ProviderGuid** ist der GUID des zu erfassenden ETW-Anbieters. Er kann anstelle von `ProviderName` verwendet werden.
 * **Ebene** legt den zu erfassenden Protokolliergrad fest. Dieser kann *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose* oder *Warning* sein.
 * **Schlüsselwörter** (Optional) Legt den ganzzahligen Wert der zu verwendenden Schlüsselwortkombinationen fest.

## <a name="use-the-trace-api-directly"></a>Direktes Verwenden der Ablaufverfolgungs-API
Sie können die API zur Application Insights-Ablaufverfolgung direkt aufrufen. Die Protokollierungsadapter verwenden diese API.

Beispiel:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

Ein Vorteil von TrackTrace ist, dass relativ lange Daten in die Nachricht eingefügt werden können. Sie können dort beispielsweise POST-Daten codieren.

Sie können Ihrer Nachricht auch einen Schweregrad hinzufügen. Wie bei anderen Telemetriedaten auch können Sie Eigenschaftswerte hinzufügen, um zu filtern oder nach verschiedenen Ablaufverfolgungen zu suchen. Beispiel:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

Auf diese Weise können Sie in der [Suche][diagnostic] alle Nachrichten eines bestimmten Schweregrads, die im Zusammenhang mit einer bestimmten Datenbank stehen, einfach herausfiltern.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler für OpenCensus Python
Mit dem Azure Monitor-Protokollhandler können Sie Python-Protokolle in Azure Monitor exportieren.

Instrumentieren Sie Ihre Anwendung mit dem [OpenCensus Python SDK](./opencensus-python.md) für Azure Monitor.

Dieses Beispiel zeigt, wie ein Protokoll auf Warnungsebene an Azure Monitor gesendet wird.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Untersuchen Ihrer Protokolle
Führen Sie Ihre App im Debugmodus aus, oder stellen Sie sie live bereit.

Wählen Sie im Übersichtsbereich im [Application Insights-Portal][portal] die Option [Suchen][diagnostic].

Sie haben beispielsweise folgende Möglichkeiten:

* Filtern nach Protokollablaufverfolgungen oder nach Elementen mit bestimmten Eigenschaften
* Untersuchen eines bestimmten Elements im Detail
* Suchen Sie nach anderen Systemprotokolldaten, die sich auf dieselbe Benutzeranforderung beziehen (mit derselben „OperationId“).
* Speichern der Konfiguration einer Seite als Favorit.

> [!NOTE]
>Wenn Ihre Anwendung eine große Menge von Daten sendet und Sie das Application Insights-SDK für ASP.NET Version 2.0.0-beta3 oder höher verwenden, wird möglicherweise die *adaptive Stichprobenerstellung* verwendet, bei der nur ein bestimmter Teil der Telemetriedaten übermittelt wird. [Erfahren Sie mehr über das Erstellen von Stichproben.](./sampling.md)
>

## <a name="troubleshooting"></a>Problembehandlung
### <a name="how-do-i-do-this-for-java"></a>Wie geht das mit Java?
In der Java-Instrumentierung ohne Code (empfohlen) werden die Protokolle standardmäßig erfasst. Verwenden Sie den [Java 3.0-Agent](./java-in-process-agent.md).

Wenn Sie das Java-SDK verwenden, verwenden Sie die [Java-Protokolladapter](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Keine Application Insights-Option im Kontextmenü des Projekts
* Stellen Sie sicher, dass die Developer Analytics Tools auf diesem Entwicklungscomputer installiert sind. Suchen Sie in Visual Studio im Menü **Extras** > **Erweiterungen und Updates** nach **Developer Analytics Tools**. Wenn die Option nicht auf der Registerkarte **Installiert** angezeigt wird, öffnen Sie die Registerkarte **Online** und installieren sie.
* Möglicherweise ist dies ein Projekttyp, der von den Developer Analytics Tools nicht unterstützt wird. Verwenden Sie die [manuelle Installation](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Es gibt keine Option für die Protokolladapter im Konfigurationstool.
* Installieren Sie zunächst das Protokollierungsframework.
* Wenn Sie „System.Diagnostics.Trace“ verwenden, müssen Sie sicherstellen, dass es [in *web.config*](/dotnet/api/system.diagnostics.eventlogtracelistener?view=dotnet-plat-ext-3.1) konfiguriert ist.
* Vergewissern Sie sich, dass die aktuelle Version von Application Insights installiert ist. Wählen Sie in Visual Studio im Menü **Extras** > **Erweiterungen und Updates** aus, und öffnen Sie die Registerkarte **Updates**. Wenn die **Developer Analytics-Tools** hier aufgeführt werden, wählen Sie sie aus, um sie zu aktualisieren.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Eine Fehlermeldung „Instrumentationsschlüssel darf nicht leer sein“ wird angezeigt.
Sie haben möglicherweise das NuGet-Paket für die Protokollierungsadapter installiert, ohne Application Insights zu installieren. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf *ApplicationInsights.config*, und wählen Sie **Application Insights aktualisieren** aus. Sie werden aufgefordert, sich bei Azure anzumelden und eine Application Insights-Ressource zu erstellen oder eine vorhandenen Ressource wiederzuverwenden. Damit sollte das Problem behoben werden.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Es werden Ablaufverfolgungen in der Diagnosesuche angezeigt, aber keine anderen Ereignisse.
Es kann eine Weile dauern, bis alle Ereignisse und Anforderungen über die Pipeline abgerufen werden.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Wie viele Daten werden beibehalten?
Die Menge der beibehaltenen Daten hängt von mehreren Faktoren ab. Weitere Informationen finden Sie auf der Seite mit den Metriken für benutzerdefinierte Ereignisse im Abschnitt [Grenzwerte](./api-custom-events-metrics.md#limits).

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Es werden einige Protokolleinträge nicht angezeigt, die ich erwartet habe.
Wenn Ihre Anwendung eine große Menge von Daten sendet und Sie das Application Insights-SDK für ASP.NET Version 2.0.0-beta3 oder höher verwenden, wird möglicherweise die adaptive Stichprobenerstellung verwendet, bei der nur ein bestimmter Teil der Telemetriedaten übermittelt wird. [Erfahren Sie mehr über das Erstellen von Stichproben.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Nächste Schritte

* [Diagnostizieren von Fehlern und Ausnahmen in ASP.NET][exceptions]
* [Weitere Informationen zur Suche][diagnostic]
* [Einrichten von Tests der Verfügbarkeit und Reaktionsfähigkeit][availability]
* [Problembehandlung][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

