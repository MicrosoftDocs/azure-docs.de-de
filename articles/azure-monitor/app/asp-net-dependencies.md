---
title: Abhängigkeitsnachverfolgung in Azure Application Insights | Microsoft Docs
description: Überwachen Sie Abhängigkeitsaufrufe von Ihrer lokalen oder Microsoft Azure-Webanwendung mit Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 479b810c5a66917bde5754d32991fb489ea26c9b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299292"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Abhängigkeitsnachverfolgung in Azure Application Insights 

Eine *Abhängigkeit* ist eine externe Komponente, die von Ihrer App aufgerufen wird. In der Regel handelt es sich um einen Dienst, der über HTTP oder eine Datenbank oder ein Dateisystem aufgerufen wird. [Application Insights](../../azure-monitor/app/app-insights-overview.md) misst Sie die Dauer von Abhängigkeitsaufrufen, gibt an, ob sie durchgeführt werden können, und stellt zusätzliche Informationen wie den Namen der Abhängigkeit etc. bereit. Sie können bestimmte Abhängigkeitsaufrufe untersuchen und sie mit Anforderungen und Ausnahmen in Zusammenhang setzen.

## <a name="automatically-tracked-dependencies"></a>Automatisch nachverfolgte Abhängigkeiten

Application Insights-SDKs für .NET und .NET Core werden mit `DependencyTrackingTelemetryModule` geliefert. Hierbei handelt es sich um ein Telemetriemodul, das Abhängigkeiten automatisch erfasst. Diese Abhängigkeitserfassung ist für [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)- und [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)-Anwendungen automatisch aktiviert, wenn diese gemäß der verknüpften offiziellen Dokumentation konfiguriert werden. `DependencyTrackingTelemetryModule` ist im Lieferumfang [dieses](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet-Pakets enthalten und wird bei Verwendung eines der NuGet-Pakete `Microsoft.ApplicationInsights.Web` oder `Microsoft.ApplicationInsights.AspNetCore` automatisch geladen.

 Folgende Abhängigkeiten werden von `DependencyTrackingTelemetryModule` derzeit automatisch verfolgt:

|Abhängigkeiten |Details|
|---------------|-------|
|Http/Https | Lokale oder Remote-HTTP/HTTPS-Aufrufe |
|WCF-Aufrufe| Wird nur dann automatisch nachverfolgt, wenn auf HTTP/HTTPS basierende Bindungen verwendet werden.|
|SQL | Aufrufe mit `SqlClient`. Informationen zum Erfassen von SQL-Abfragen finden Sie [hier](##advanced-sql-tracking-to-get-full-sql-query).  |
|[Azure-Speicher (Blob, Tabelle und Warteschlange)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Aufrufe mit Azure Storage-Client. |
|[EventHub-Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 und höher. |
|[ServiceBus-Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 und höher. |
|Azure Cosmos DB | Wird nur dann automatisch nachverfolgt, wenn HTTP/HTTPS verwendet wird. Der TCP-Modus wird von Application Insights nicht erfasst. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Einrichten einer automatischen Abhängigkeitsüberwachung in Konsolen-Apps

Um Abhängigkeiten automatisch über .NET-/.NET Core-Konsolen-Apps nachzuverfolgen, installieren Sie das Nuget-Paket `Microsoft.ApplicationInsights.DependencyCollector`, und initialisieren Sie `DependencyTrackingTelemetryModule` wie folgt:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Funktionsweise der automatischen Abhängigkeitsüberwachung

Abhängigkeiten werden über eine der folgenden Methoden automatisch gesammelt:

* Über die Bytecodeinstrumentierung um ausgewählte Methoden (InstrumentationEngine entweder aus StatusMonitor oder der Azure-Web-App-Erweiterung)
* EventSource-Rückrufe
* DiagnosticSource-Rückrufe (in den neuesten.NET-/.NET Core-SDKs)

## <a name="manually-tracking-dependencies"></a>Manuelle Nachverfolgung von Abhängigkeiten

Es folgen einige Beispiele für Abhängigkeiten, die nicht automatisch erfasst werden und daher eine manuelle Nachverfolgung erfordern.

* Azure Cosmos DB wird nur dann automatisch nachverfolgt, wenn [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) verwendet wird. Der TCP-Modus wird von Application Insights nicht erfasst.
* Redis

Abhängigkeiten, die nicht automatisch vom SDK erfasst werden, können Sie manuell mithilfe der [TrackDependency-API](api-custom-events-metrics.md#trackdependency) nachverfolgen, die von den Standardmodulen zur automatischen Erfassung verwendet wird.

Beispiel: Wenn Sie Ihren Code mit einer Assembly erstellen, die Sie nicht selbst geschrieben haben, könnten Sie die Zeit aller Aufrufe ermitteln, um herauszufinden, welchen Beitrag sie an Ihren Reaktionszeiten hat. Um diese Daten in den Abhängigkeitsdiagrammen in Application Insights anzuzeigen, senden Sie sie mit `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternativ stellt `TelemetryClient` die Erweiterungsmethoden `StartOperation` und `StopOperation` bereit, die zum manuellen Nachverfolgen von Abhängigkeiten verwendet werden können, wie [hier](custom-operations-tracking.md#outgoing-dependencies-tracking) gezeigt.

Wenn Sie das Standardmodul für die Nachverfolgung von Abhängigkeiten deaktivieren möchten, entfernen Sie für ASP.NET-Anwendungen den Verweis auf DependencyTrackingTelemetryModule in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Befolgen Sie für ASP.NET Core-Anwendungen die [hier](asp-net-core.md#configuring-or-removing-default-telemetrymodules) aufgeführten Anweisungen.

## <a name="tracking-ajax-calls-from-web-pages"></a>Nachverfolgen von AJAX-Aufrufen von Webseiten

Für Webseiten erfasst das JavaScript-SDK von Application Insights AJAX-Aufrufe automatisch als Abhängigkeiten, wie [hier](javascript.md#ajax-performance) beschrieben. Dieses Dokument konzentriert sich auf Abhängigkeiten von Serverkomponenten.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Erweiterte SQL-Nachverfolgung zum Abrufen vollständiger SQL-Abfragen

Für SQL-Aufrufe wird der Name des Servers und der Datenbank immer erfasst und als Name der erfassten `DependencyTelemetry` gespeichert. Es gibt ein zusätzliches Feld namens „data“, das den vollständigen Text der SQL-Abfrage enthalten kann.

Für ASP.NET Core-Anwendungen ist kein zusätzlicher Schritt erforderlich, um die vollständige SQL-Abfrage abzurufen.

Bei ASP.NET-Anwendungen wird die vollständige SQL-Abfrage mithilfe der Bytecodeinstrumentierung erfasst. Hierzu ist die Instrumentierungs-Engine erforderlich. Zudem sind zusätzliche plattformspezifische Schritte nötig, die unten beschrieben werden.

| Plattform | Erforderliche Schritte zum Abrufen der vollständigen SQL-Abfrage |
| --- | --- |
| Azure-Web-App |In der Systemsteuerung Ihrer Web-App [öffnen Sie das Application Insights-Blatt](../../azure-monitor/app/azure-web-apps.md), und aktivieren Sie SQL-Befehle unter .NET. |
| IIS-Server (Azure-VM, lokal usw.) | [Installieren Sie den Statusmonitor auf Ihrem Server, auf dem die Anwendung ausgeführt wird](../../azure-monitor/app/monitor-performance-live-website-now.md), und starten Sie IIS neu.
| Azure Cloud Service |[Verwenden Sie den Starttask](../../azure-monitor/app/cloudservices.md) zum [Installieren des Statusmonitors](monitor-performance-live-website-now.md#download). |
| IIS Express | Nicht unterstützt

In den oben genannten Fällen können Sie die ordnungsgemäße Installation der Instrumentierungs-Engine überprüfen, indem Sie sicherstellen, dass die SDK-Version der erfassten `DependencyTelemetry` „rddp“ lautet. „rdddsd“ oder „rddf“ weisen darauf hin, dass Abhängigkeiten über DiagnosticSource- oder EventSource-Rückrufe gesammelt werden und die vollständige SQL-Abfrage daher nicht erfasst wird.

## <a name="where-to-find-dependency-data"></a>Hier finden Sie Abhängigkeitsdaten

* [Anwendungszuordnung](app-map.md) visualisiert Abhängigkeiten zwischen Ihrer App und angrenzenden Komponenten.
* Die [Transaktionsdiagnose](transaction-diagnostics.md) zeigt einheitliche, korrelierte Serverdaten.
* [Das Blatt „Browser“](javascript.md#ajax-performance) zeigt AJAX-Aufrufe von Browsern Ihrer Benutzer.
* Navigieren Sie zu langsamen oder fehlgeschlagenen Aufrufen, um ihre Abhängigkeitsaufrufe zu überprüfen.
* [Analyse](#analytics) kann verwendet werden, um Abhängigkeitsdaten abzufragen.

## <a name="diagnosis"></a>Diagnostizieren langsamer Anforderungen

Jedes Anforderungsereignis bezieht sich auf Abhängigkeitsaufrufe, Ausnahmen und andere Ereignisse, die nachverfolgt werden, während Ihre App die Anforderung verarbeitet. Wenn einige Anforderungen also eine schlechte Leistung zeigen, können Sie herausfinden, ob es an langsamen Antworten einer Abhängigkeit liegt.

Wir sehen uns nun ein Beispiel dazu an.

### <a name="tracing-from-requests-to-dependencies"></a>Ablaufverfolgung von Anforderungen bis Abhängigkeiten

Öffnen Sie das Blatt „Performance“, und betrachten Sie das Raster der Anforderungen:

![Liste der Anforderungen mit Mittelwerten und Anzahlen](./media/asp-net-dependencies/02-reqs.png)

Die zuerst aufgeführte Instanz dauert lange. Mal sehen, ob wir herausfinden, wo die Zeit beansprucht wird.

Klicken Sie auf diese Zeile, um einzelne Anforderungsereignisse anzuzeigen:

![Liste der Anforderungsinstanzen](./media/asp-net-dependencies/03-instances.png)

Klicken Sie auf eine beliebige Instanz mit langer Ausführungsdauer, um diese näher zu überprüfen, und scrollen Sie nach unten zu den Remoteabhängigkeitsaufrufen, die im Zusammenhang mit dieser Anforderung bestehen:

![Finden von Aufrufen von Remoteabhängigkeiten, Identifizieren ungewöhnlich langer Laufzeiten](./media/asp-net-dependencies/04-dependencies.png)

Der Großteil der Zeit zur Verarbeitung dieser Anforderung wurde für einen Aufruf eines lokalen Diensts aufgewendet.

Wählen Sie diese Zeile aus, um weitere Informationen zu erhalten:

![Klicken Sie sich durch diese Remoteabhängigkeit, um die Ursache herauszufinden](./media/asp-net-dependencies/05-detail.png)

Offenbar liegt das Problem bei dieser Abhängigkeit. Nachdem das Problem jetzt identifiziert ist, müssen wir noch herausfinden, warum dieser Aufruf so lange dauert.

### <a name="request-timeline"></a>Anfordern der Zeitachse

In einem anderen Fall handelt es sich nicht um einen Abhängigkeitsaufruf mit langer Ausführungsdauer. Wenn wir aber zur Zeitachsenansicht wechseln, können wir sehen, wo die Verzögerung in unserer internen Verarbeitung aufgetreten ist:

![Finden von Aufrufen von Remoteabhängigkeiten, Identifizieren ungewöhnlich langer Laufzeiten](./media/asp-net-dependencies/04-1.png)

Es scheint eine große Unterbrechung nach dem ersten Abhängigkeitsaufruf zu geben, daher sollten wir den Code betrachten, um den Grund dafür herauszufinden.

### <a name="profile-your-live-site"></a>Erstellen eines Profils Ihrer Livewebsite

Sie möchten wissen, was am längsten gedauert hat? Der [Application Insights-Profiler](../../azure-monitor/app/profiler.md) verfolgt HTTP-Aufrufe zu Ihrer Livewebsite zurück und zeigt an, welche Funktionen im Code die meiste Zeit in Anspruch genommen haben.

## <a name="failed-requests"></a>Anforderungsfehler

Anforderungsfehler können auch fehlgeschlagenen Aufrufen von Abhängigkeiten zugeordnet werden. Wir können erneut bis zum Problem durchklicken.

![Klicken Sie auf das Diagramm mit Anforderungsfehlern](./media/asp-net-dependencies/06-fail.png)

Klicken Sie auf einen Anforderungsfehler, und sehen Sie sich die zugeordneten Ereignisse an.

![Klicken Sie auf den Anforderungstyp, klicken Sie auf die Instanz, um eine andere Ansicht derselben Instanz abzurufen, und klicken Sie darauf, um Details zur Ausnahme zu erhalten.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Analytics

Sie können Abhängigkeiten in der [Abfragesprache Kusto](/azure/kusto/query/) verfolgen. Hier einige Beispiele.

* Suchen fehlgeschlagener Abhängigkeitsaufrufe:

```

    dependencies | where success != "True" | take 10
```

* Suchen von AJAX-Aufrufen:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Suchen von mit Anforderungen verbundenen Abhängigkeitsaufrufen:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Suchen von mit Seitenaufrufen verbundenen AJAX-Aufrufen:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Wie meldet die automatische Abhängigkeitserfassung fehlerhafte Aufrufe an Abhängigkeiten?*

* Bei fehlerhaften Abhängigkeitsaufrufen wird das Feld „success“ auf FALSE festgelegt. `DependencyTrackingTelemetryModule` gibt keine Auskunft über `ExceptionTelemetry`. Das vollständige Datenmodell für Abhängigkeiten wird [hier](data-model-dependency-telemetry.md) beschrieben.

## <a name="open-source-sdk"></a>Open Source SDK
Wie jedes Application Insights-SDK ist auch das Modul zur Abhängigkeitserfassung ein Open Source-Modul. Lesen Sie den Code, tragen Sie zum Code bei, oder melden Sie Issues im [offiziellen GitHub-Repository](https://github.com/Microsoft/ApplicationInsights-dotnet-server).


## <a name="next-steps"></a>Nächste Schritte

* [Ausnahmen](../../azure-monitor/app/asp-net-exceptions.md)
* [Daten zu Seiten und Benutzern](../../azure-monitor/app/javascript.md)
* [Availability](../../azure-monitor/app/monitor-web-app-availability.md)
