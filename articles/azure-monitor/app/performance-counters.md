---
title: Leistungsindikatoren in Application Insights | Microsoft Docs
description: Überwachen Sie systemeigene und benutzerdefinierte .NET-Leistungsindikatoren in Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: 0ec64a5ae412fb4a1900021fefcb7d9112b1b019
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255339"
---
# <a name="system-performance-counters-in-application-insights"></a>Systemleistungsindikatoren in Application Insights

Windows bietet eine Vielzahl von [Leistungsindikatoren](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) wie z.B. CPU-Belegung, Arbeitsspeicher, Datenträger und Netzwerkverwendung. Sie können auch eigene Leistungsindikatoren definieren. Die Erfassung von Leistungsindikatoren wird unterstützt, sofern Ihre Anwendung unter IIS auf einem lokalen Host oder auf einem virtuellen Computer ausgeführt wird, auf den Sie Administratorzugriff haben. Für Anwendungen, die als Azure-Web-Apps ausgeführt werden, besteht zwar kein direkter Zugriff auf Leistungsindikatoren, aber eine Teilmenge der verfügbaren Indikatoren wird von Application Insights erfasst.

## <a name="view-counters"></a>Anzeigen von Indikatoren

Im Bereich „Metriken“ wird der Standardsatz von Leistungsindikatoren angezeigt.

![In Application Insights gemeldete Leistungsindikatoren](./media/performance-counters/performance-counters.png)

Hier sind die Standardleistungsindikatoren angegeben, für die derzeit die Erfassung für ASP.NET/ASP.NET Core-Webanwendungen konfiguriert ist:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

## <a name="add-counters"></a>Hinzufügen von Leistungsindikatoren

Wenn der gewünschte Leistungsindikator nicht in der Liste der Metriken enthalten ist, können Sie ihn hinzufügen.

1. Welche Leistungsindikatoren in Ihrem Server verfügbar sind, können Sie ermitteln, indem Sie diesen PowerShell-Befehl auf dem lokalen Server verwenden:

    `Get-Counter -ListSet *`

    (Informationen hierzu finden Sie unter [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Öffnen Sie "ApplicationInsights.config".

   * Wenn Sie Application Insights während der Entwicklung zu Ihrer Anwendung hinzugefügt haben, bearbeiten Sie die Datei „ApplicationInsights.config“ in Ihrem Projekt und stellen Sie sie anschließend erneut auf Ihren Servern bereit.
3. Bearbeiten Sie Leistungserfassungsanweisung:

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> Da ASP.NET Core-Anwendungen nicht über `ApplicationInsights.config` verfügen, ist die obige Methode für ASP.NET Core-Anwendungen nicht gültig.

Sie können sowohl Standardindikatoren als auch Indikatoren erfassen, die Sie selbst implementiert haben. `\Objects\Processes` ist ein Beispiel für einen Standardindikator, der auf allen Windows-Systemen verfügbar ist. `\Sales(photo)\# Items Sold` ist ein Beispiel für einen benutzerdefinierten Indikator, der in einem Webdienst implementiert sein kann.

Das Format lautet `\Category(instance)\Counter"` bzw. für Kategorien, die keine Instanzen besitzen, einfach `\Category\Counter`.

`ReportAs` ist für Leistungsindikatornamen erforderlich, die mit diesen Zeichen `[a-zA-Z()/-_ \.]+` nicht übereinstimmen, d.h., sie enthalten Zeichen, die nicht zu folgenden Gruppen zählen: Buchstaben, runde Klammern, Schrägstrich, Bindestrich, Unterstrich, Leerzeichen, Punkt.

Wenn Sie eine Instanz angeben, wird sie als CounterInstanceName-Dimension der gemeldeten Metrik erfasst.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Erfassen von Leistungsindikatoren im Code für ASP.NET-Webanwendungen oder .NET/.NET Core-Konsolenanwendungen
Um Systemleistungsindikatoren zu erfassen und an Application Insights zu senden, können Sie den folgenden Codeausschnitt anpassen:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Alternativ können Sie dieselben Schritte mit von Ihnen erstellten benutzerdefinierten Metriken ausführen:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Erfassen von Leistungsindikatoren im Code für ASP.NET Core-Webanwendungen

Ändern Sie die `ConfigureServices`-Methode in Ihrer `Startup.cs`-Klasse wie unten angegeben.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Leistungsindikatoren in Analytics
In [Analytics](../../azure-monitor/app/analytics.md) können Sie nach Leistungsindikatorberichten suchen und diese anzeigen.

Das Schema **performanceCounters** zeigt die `category`, den `counter`-Namen und `instance`-Namen der einzelnen Leistungsindikatoren.  In den Telemetriedaten jeder Anwendung werden nur die Indikatoren für diese Anwendung angezeigt. Beispielsweise, um verfügbare Leistungsindikatoren anzuzeigen: 

![Leistungsindikatoren in der Application Insights-Analyse](./media/performance-counters/analytics-performance-counters.png)

('Instance' bezieht sich hier auf die Instanz des Leistungsindikators, nicht auf die Instanz der Rolle oder des Server-Computers. Leistungsindikatoren wie etwa die Prozessorzeit werden vom Namen der Leistungsindikatorinstanz in der Regel nach dem Namen des Prozesses oder der Anwendung segmentiert.)

So erhalten Sie ein Diagramm des verfügbaren Arbeitsspeichers im aktuellen Zeitraum: 

![Zeitdiagramm der Speicherauslastung in der Application Insights-Analyse](./media/performance-counters/analytics-available-memory.png)

Wie andere Telemetriedaten umfasst auch **performanceCounters** eine Spalte `cloud_RoleInstance`, die die Identität der Hostserverinstanz angibt, auf dem Ihre Anwendung ausgeführt wird. Geben Sie beispielsweise Folgendes ein, um die Leistung Ihrer App auf verschiedenen Computern vergleichen: 

![Nach Rolleninstanz in der Application Insights-Analyse segmentierte Leistung](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET- und Application Insights-Zahlen

*Worin besteht der Unterschied zwischen der Ausnahmerate und der Ausnahmenmetrik?*

* *Ausnahmerate* ist ein Systemleistungsindikator. Die CLR zählt alle behandelten und nicht behandelten Ausnahmen, die ausgelöst werden, und dividiert das Ergebnis innerhalb eines Samplingintervalls durch die Länge dieses Intervalls. Das Application Insights SDK sammelt dieses Ergebnis und sendet es an das Portal.

* *Ausnahmen* ist die Anzahl der TrackException-Meldungen, die das Portal innerhalb des Samplingintervalls des Diagramms empfangen hat. Sie enthält nur die behandelten Ausnahmen, wo Sie TrackException-Aufrufe in Ihren Code geschrieben haben, und enthält nicht alle [nicht behandelten Ausnahmen](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Leistungsindikatoren für Anwendungen, die in Azure-Web-Apps ausgeführt werden

Sowohl ASP.NET- als auch ASP.NET Core-Anwendungen, die in Azure-Web-Apps bereitgestellt werden, werden in einer speziellen Sandkastenumgebung ausgeführt. Für diese Umgebung ist der direkte Zugriff auf Systemleistungsindikatoren nicht möglich. Es wird aber eine eingeschränkte Teilmenge der Indikatoren in Form von Umgebungsvariablen verfügbar gemacht. Dies ist [hier](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables) beschrieben. Mit dem Application Insights SDK für ASP.NET und ASP.NET Core werden Leistungsindikatoren aus Azure-Web-Apps aus diesen speziellen Umgebungsvariablen erfasst. In dieser Umgebung ist nur eine Teilmenge der Indikatoren verfügbar. Die vollständige Liste finden Sie [hier](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs).

## <a name="performance-counters-in-aspnet-core-applications"></a>Leistungsindikatoren in ASP.NET Core-Anwendungen

* Mit dem [ASP.NET Core SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) Version 2.4.1 und höher werden Leistungsindikatoren erfasst, wenn die Anwendung per Azure-Web-App (Windows) ausgeführt wird.

* Mit SDK Version 2.7.0-beta3 und höher werden Leistungsindikatoren erfasst, wenn die Anwendung unter Windows ausgeführt wird und auf `NETSTANDARD2.0` oder höher ausgerichtet ist.
* Für Anwendungen, die für das .NET Framework bestimmt sind, werden Leistungsindikatoren in allen Versionen des SDK unterstützt.
* Dieser Artikel wird aktualisiert, wenn die Unterstützung von Leistungsindikatoren für andere Betriebssysteme als Windows hinzugefügt wird.

## <a name="alerts"></a>Alerts
Wie bei anderen Metriken können Sie [eine Warnung festlegen](../../azure-monitor/app/alerts.md), damit Sie gewarnt werden, wenn ein Leistungsindikator einen von Ihnen festgelegten Grenzwert überschreitet. Öffnen Sie den Bereich „Warnungen“, und klicken Sie auf „Warnung hinzufügen“.

## <a name="next"></a>Nächste Schritte

* [Abhängigkeitsüberwachung](../../azure-monitor/app/asp-net-dependencies.md)
* [Ausnahmeverfolgung](../../azure-monitor/app/asp-net-exceptions.md)

