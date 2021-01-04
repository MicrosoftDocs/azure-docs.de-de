---
title: Application Insights-API für benutzerdefinierte Ereignisse und Metriken | Microsoft Docs
description: Fügen Sie einige Codezeilen in Ihrer Geräte- oder Desktop-App, Webseite oder dem Webdienst ein, um Nutzungs- und Diagnoseprobleme nachzuverfolgen.
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ae64888669fb9a3c053802ee4f7ad7db6316265d
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780500"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights-API für benutzerdefinierte Ereignisse und Metriken

Fügen Sie einige Codezeilen in Ihre Anwendung ein, um herauszufinden, wie sie von Benutzern eingesetzt wird, oder um Probleme zu diagnostizieren. Sie können Telemetriedaten von Geräte- und Desktop-Apps, Webclients und Webservern senden. Verwenden Sie die [Azure Application Insights](./app-insights-overview.md)-Kerntelemetrie-API, um benutzerdefinierte Ereignisse und Metriken und Ihre eigenen Versionen der standardmäßigen Telemetrie zu senden. Dies ist die gleiche API, die von den standardmäßigen Application Insights-Datensammlern verwendet wird.

## <a name="api-summary"></a>API-Zusammenfassung

Die Haupt-API ist, abgesehen von einigen Abweichungen wie `GetMetric` (nur .NET), auf allen Plattformen einheitlich.

| Methode | Syntaxelemente |
| --- | --- |
| [`TrackPageView`](#page-views) |Seiten, Bildschirme, Blätter oder Formulare. |
| [`TrackEvent`](#trackevent) |Benutzeraktionen und andere Ereignisse. Wird zum Verfolgen des Benutzerverhaltens oder zur Leistungsüberwachung eingesetzt. |
| [`GetMetric`](#getmetric) |Nullmetriken und mehrdimensionale Metriken, zentral konfigurierte Aggregation, nur C#. |
| [`TrackMetric`](#trackmetric) |Leistungsmessungen wie Warteschlangenlängen, die nicht im Zusammenhang mit bestimmten Ereignissen stehen. |
| [`TrackException`](#trackexception) |Protokollieren von Ausnahmen für die Diagnose. Verfolgen Sie, wo diese in Bezug auf andere Ereignisse auftreten, und untersuchen Sie die Stapelüberwachung. |
| [`TrackRequest`](#trackrequest) |Protokollieren der Häufigkeit und Dauer der Serveranforderungen für die Leistungsanalyse. |
| [`TrackTrace`](#tracktrace) |Protokollnachrichten zur Ressourcendiagnose. Sie können auch Drittanbieterprotokolle erfassen. |
| [`TrackDependency`](#trackdependency) |Protokollieren der Dauer und Häufigkeit der Aufrufe von externen Komponenten, von denen Ihre Anwendung abhängt. |

Sie können den meisten dieser Telemetrieaufrufe [Eigenschaften und Metriken anfügen](#properties) .

## <a name="before-you-start"></a><a name="prep"></a>Vorbereitung

Gehen Sie wie folgt vor, falls Sie noch nicht über einen Verweis auf das Application Insights SDK verfügen:

* Fügen Sie Ihrem Projekt das Application Insights-SDK hinzu:

  * [ASP.NET-Projekt](./asp-net.md)
  * [ASP.NET Core-Projekt](./asp-net-core.md)
  * [Java-Projekt](./java-get-started.md)
  * [Node.js-Projekt](./nodejs.md)
  * [JavaScript auf jeder Webseite](./javascript.md) 
* Schließen Sie Folgendes in den Geräte- oder Webservercode ein:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Abrufen einer TelemetryClient-Instanz

Rufen Sie eine `TelemetryClient`-Instanz ab (gilt nicht für JavaScript auf Webseiten):

Für [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected)-Apps und [Nicht-HTTP/Worker für .NET/.NET Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected)-Apps empfiehlt es sich, eine Instanz von `TelemetryClient` aus dem Container für die Abhängigkeitsinjektion abzurufen, wie in der entsprechenden Dokumentation erläutert.

Halten Sie sich bei Verwendung von AzureFunctions v2 (oder höher) oder Azure WebJobs v3 (oder höher) an folgendes Dokument: https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Sollten Meldungen mit dem Hinweis angezeigt werden, dass diese Methode veraltet ist, besuchen Sie [microsoft/ApplicationInsights-dotnet#1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152), um weitere Informationen zu erhalten.

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient ist threadsicher.

Bei ASP.NET- und Java-Projekten werden eingehende HTTP-Anforderungen automatisch erfasst. Es empfiehlt sich unter Umständen, zusätzliche TelemetryClient-Instanzen für weitere Module Ihrer App zu erstellen. So können Sie beispielsweise eine TelemetryClient-Instanz in Ihrer Middleware-Klasse verwenden, um Geschäftslogikereignisse zu melden. Sie können Eigenschaften wie „UserId“ und „DeviceId“ festlegen, um den Computer zu identifizieren. Diese Informationen werden an alle Ereignissen angefügt, die von der Instanz gesendet werden.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

In Node.js-Projekten können Sie `new applicationInsights.TelemetryClient(instrumentationKey?)` verwenden, um eine neue Instanz zu erstellen, aber diese Empfehlung gilt nur für Szenarien, die eine vom Singleton-`defaultClient` isolierte Konfiguration erfordern.

## <a name="trackevent"></a>TrackEvent

In Application Insights handelt es sich bei einem *benutzerdefinierten Ereignis* um einen Datenpunkt, den Sie als aggregierte Anzahl im [Metrik-Explorer](../platform/metrics-charts.md) und als einzelne Vorkommen in der [Diagnosesuche](./diagnostic-search.md) anzeigen können. (Er gehört nicht zu MVC oder anderen Ereignissen des Frameworks.)

Fügen Sie `TrackEvent`-Aufrufe in Ihrem Code ein, um verschiedene Ereignisse zu zählen – beispielsweise, wie oft Benutzer ein bestimmtes Feature auswählen, bestimmte Ziele erreichen oder bestimmte Arten von Fehlern machen.

Senden Sie z. B. in einer Spiele-App ein Ereignis, sobald ein Benutzer das Spiel gewinnt:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Benutzerdefinierte Ereignisse in Analytics

Die Telemetrie ist in der Tabelle `customEvents` in [Application Insights Analytics](../log-query/log-query-overview.md) verfügbar. Jede Zeile stellt einen Aufruf von `trackEvent(..)` in der App dar.

Wenn die [Stichprobenentnahme](./sampling.md) aktiv ist, wird für die itemCount-Eigenschaft ein Wert größer 1 angezeigt. itemCount==10 bedeutet beispielsweise, dass bei der Stichprobenentnahme von 10 Aufrufen von trackEvent() nur einer übertragen wurde. Zum Abrufen der richtigen Anzahl benutzerdefinierter Ereignisse sollten Sie daher Code wie `customEvents | summarize sum(itemCount)` verwenden.

## <a name="getmetric"></a>GetMetric

Informationen zur effektiven Verwendung des GetMetric()-Aufrufs zum Erfassen lokal vorab aggregierter Metriken für .NET- und .NET Core-Anwendungen finden Sie in der Dokumentation zu [GetMetric](./get-metric.md).

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> 2Microsoft.ApplicationInsights.TelemetryClient.TrackMetric“ ist nicht die bevorzugte Methode für das Senden von Metriken. Metriken sollten immer für einen bestimmten Zeitraum vorab aggregiert werden, bevor sie gesendet werden. Verwenden Sie eine der GetMetric(..)-Überladungen, um ein Metrikobjekt für den Zugriff auf Funktionen für die SDK-Vorabaggregation zu erhalten. Wenn Sie Ihre eigene Logik für die Vorabaggregation implementieren, können Sie die TrackMetric()-Methode zum Senden der sich ergebenden Aggregate verwenden. Falls für Ihre Anwendung das Senden eines separaten Telemetrieelements immer erforderlich ist (ohne zeitabhängige Aggregation), verfügen Sie wahrscheinlich über einen Anwendungsfall für eine Ereignistelemetrie. Weitere Informationen finden Sie unter „TelemetryClient.TrackEvent (Microsoft.Applicationlnsights.DataContracts.EventTelemetry)“.

In Application Insights können Metriken dargestellt werden, die keinen bestimmten Ereignissen zugeordnet sind. Beispielsweise könnten Sie die Länge einer Warteschlange in regelmäßigen Abständen überwachen. Bei vorhandenen Metriken sind die einzelnen Messwerte von geringerem Interesse als die Abwandlungen und Trends, daher sind statistische Diagramme sehr nützlich.

Zum Senden von Metriken an Application Insights können Sie die `TrackMetric(..)`-API verwenden. Es gibt zwei Möglichkeiten, eine Metrik zu senden:

* Einzelner Wert: Bei jeder Durchführung einer Messung in der Anwendung wird der entsprechende Wert an Application Insights gesendet. Angenommen, Sie haben eine Metrik für die Anzahl der Elemente in einem Container festgelegt. In einem bestimmten Zeitraum fügen Sie dem Container zunächst drei Elemente hinzu und entfernen dann zwei Elemente. Dementsprechend rufen Sie `TrackMetric` zweimal auf: zuerst zum Übergeben des Werts `3` und anschließend zum Übergeben des Werts `-2`. Application Insights speichert beide Werte für Sie.

* Aggregation: Bei der Arbeit mit Metriken ist nur selten jede einzelne Messung von Interesse. Stattdessen ist eine Zusammenfassung der Vorgänge in einem bestimmten Zeitraum wichtig. Eine solche Zusammenfassung wird als _Aggregation_ bezeichnet. Im obigen Beispiel lautet die aggregierte Metriksumme für den Zeitraum `1`, und die Anzahl von Metrikwerten ist `2`. Bei Verwendung der Aggregation rufen Sie `TrackMetric` nur einmal pro Zeitraum auf und senden die aggregierten Werte. Diese Vorgehensweise empfiehlt sich, um die Kosten und den Leistungsaufwand erheblich zu reduzieren, da weniger Datenpunkte an Application Insights gesendet, aber dennoch alle relevanten Informationen erfasst werden.

### <a name="examples"></a>Beispiele

#### <a name="single-values"></a>Einzelne Werte

So senden Sie einen einzelnen Metrikwert:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Benutzerdefinierte Metriken in der Analyse

Die Telemetrie ist in der Tabelle `customMetrics` in [Application Insights Analytics](../log-query/log-query-overview.md) verfügbar. Jede Zeile stellt einen Aufruf von `trackMetric(..)` in der App dar.

* `valueSum`: Dies ist die Summe der Messwerte. Den Mittelwert erhalten Sie, indem Sie eine Division durch `valueCount` ausführen.
* `valueCount`: Die Anzahl der Messwerte, die in diesem `trackMetric(..)`-Aufruf aggregiert wurden.

## <a name="page-views"></a>Seitenaufrufe

In einer Geräte- oder Webseiten-App werden die Telemetriedaten zu den Seitenaufrufen standardmäßig gesendet, wenn die einzelnen Bildschirme oder Seiten geladen werden. Sie können dies jedoch so ändern, dass Seitenaufrufe zu zusätzlichen oder anderen verfolgt werden. Angenommen, Sie möchten in einer App mit Registerkarten oder Blättern eine „Seite“ nachverfolgen, sobald der Benutzer ein neues Blatt öffnet.

Benutzer- und Sitzungsdaten werden als Eigenschaften zusammen mit Seitenaufrufen gesendet, damit die Benutzer- und Sitzungsdiagramme aktiv werden, sobald Telemetriedaten zu den Seitenaufrufen vorliegen.

### <a name="custom-page-views"></a>Benutzerdefinierte Seitenaufrufe

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Wenn Sie mehrere Registerkarten in anderen HTML-Seiten haben, können Sie ebenfalls die URL angeben:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Zeitliches Einteilen von Seitenaufrufen

In der Standardeinstellung werden die gemeldeten Zeiten für **Ladezeit der Seitenansicht** von dem Zeitpunkt, zu dem der Browser die Anforderung sendet, bis zum Aufruf des Seitenladeereignisses im Browser gemessen.

Sie haben stattdessen noch folgende Möglichkeiten:

* Legen Sie im [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview)-Aufruf eine explizite Dauer fest: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Verwenden Sie die Zeitsteuerungsaufrufe `startTrackPage` und `stopTrackPage` für die Seitenansicht.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Mit dem Namen, den Sie für den ersten Parameter verwenden, werden die Start- und Stoppaufrufe zugeordnet. Der Standardwert ist der Name der aktuellen Seite.

Die resultierenden Zeiten für das Laden der Seite, die im Metrik-Explorer angezeigt werden, leiten sich vom Intervall zwischen den Start- und Stoppaufrufen ab. Sie können entscheiden, welches Intervall Sie tatsächlich verwenden.

### <a name="page-telemetry-in-analytics"></a>Seitentelemetrie in Analytics

In [Analytics](../log-query/log-query-overview.md) werden Daten aus Browservorgängen in zwei Tabellen angezeigt:

* Die Tabelle `pageViews` enthält Daten über die URL und den Seitentitel.
* Die Tabelle `browserTimings` enthält Daten über die Leistung des Clients, z.B. die angefallene Zeit zum Verarbeiten der eingehenden Daten.

So ermitteln Sie, wie lange die Verarbeitung unterschiedlicher Seiten im Browser dauert

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

So ermitteln Sie die Beliebtheit verschiedener Browser

```kusto
pageViews
| summarize count() by client_Browser
```

Um Seitenansichten AJAX-Aufrufen zuzuordnen, verknüpfen Sie sie mit Abhängigkeiten:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Für das Server-SDK wird TrackRequest zum Protokollieren von HTTP-Anforderungen verwendet.

Sie können diese Methode auch selbst aufrufen, wenn Sie Anforderungen in einem Kontext simulieren möchten, in dem das Webdienstmodul nicht ausgeführt wird.

Es wird jedoch empfohlen, Telemetrieanforderungen zu senden, bei denen die Anforderung als <a href="#operation-context">Vorgangskontext</a> fungiert.

## <a name="operation-context"></a>Vorgangskontext

Sie können Telemetrieelemente zusammen korrelieren, indem Sie sie dem Vorgangskontext zuordnen. Das standardmäßige Anforderungsnachverfolgungs-Modul führt dies für Ausnahmen und andere Ereignisse aus, die beim Verarbeiten einer HTTP-Anforderung gesendet werden. Unter [Suche](./diagnostic-search.md) und [Analyse](../log-query/log-query-overview.md) können Sie anhand der Vorgangs-ID leicht Ereignisse finden, die der Anforderung zugeordnet sind.

Ausführlichere Informationen zur Korrelation finden Sie unter [Telemetriekorrelation in Application Insights](./correlation.md).

Beim manuellen Nachverfolgen der Telemetriedaten besteht die einfachste Vorgehensweise darin, die Korrelation der Telemetriedaten sicherzustellen, indem das folgende Muster verwendet wird:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Neben dem Festlegen eines Vorgangskontexts wird mit `StartOperation` ein Telemetrieelement des von Ihnen angegebenen Typs erstellt. Das Telemetrieelement wird gesendet, wenn Sie den Vorgang verwerfen oder wenn Sie `StopOperation` explizit aufrufen. Wenn Sie `RequestTelemetry` als Telemetrietyp verwenden, wird die Dauer („Duration“) auf das Zeitintervall zwischen Start und Stopp festgelegt.

Die Telemetrieelemente, die innerhalb eines Vorgangsbereichs gemeldet werden, werden zu den untergeordneten Elementen des Vorgangs. Vorgangskontexte können geschachtelt werden.

In der Suche wird mit dem Vorgangskontext die Liste **Verwandte Elemente** erstellt:

![Verwandte Elemente](./media/api-custom-events-metrics/21.png)

Weitere Informationen zur Nachverfolgung benutzerdefinierter Vorgänge finden Sie unter [Nachverfolgen von benutzerdefinierten Vorgängen mit dem Application Insights .NET SDK](./custom-operations-tracking.md).

### <a name="requests-in-analytics"></a>Anforderungen in Analytics

In [Application Insights Analytics](../log-query/log-query-overview.md) werden Anforderungen in der Tabelle `requests` angezeigt.

Wenn die [Stichprobenentnahme](./sampling.md) aktiv ist, wird für die itemCount-Eigenschaft ein Wert größer 1 angezeigt. itemCount==10 bedeutet beispielsweise, dass bei der Stichprobenentnahme von 10 Aufrufen von trackRequest() nur einer übertragen wurde. Um die richtige Anzahl der Anforderungen und die durchschnittliche Dauer nach Anforderungsnamen segmentiert abzurufen, verwenden Sie beispielsweise folgenden Code:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Ziele beim Senden von Ausnahmen an Application Insights:

* Durchführen einer [Zählung](../platform/metrics-charts.md) als Hinweis auf die Häufigkeit eines Problems
* [Untersuchen einzelner Vorkommen](./diagnostic-search.md)

Die Berichte enthalten die Stapelüberwachung.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Die SDKs viele Ausnahmen automatisch abfangen, müssen Sie TrackException nicht immer explizit aufrufen.

* ASP.NET: [Schreiben Sie Code zum Abfangen von Ausnahmen](./asp-net-exceptions.md).
* Java EE: [Ausnahmen werden automatisch abgefangen](./java-get-started.md#exceptions-and-request-failures).
* JavaScript: Ausnahmen werden automatisch abgefangen. Wenn Sie die automatische Erfassung deaktivieren möchten, können Sie dem Codeausschnitt, den Sie in Ihren Webseiten einfügen, eine Zeile hinzufügen:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Ausnahmen in Analytics

In [Application Insights Analytics](../log-query/log-query-overview.md) werden Ausnahmen in der Tabelle `exceptions` angezeigt.

Wenn die [Stichprobenentnahme](./sampling.md) aktiv ist, wird für die Eigenschaft `itemCount` ein Wert größer 1 angezeigt. itemCount==10 bedeutet beispielsweise, dass bei der Stichprobenentnahme von 10 Aufrufen von trackException() nur einer übertragen wurde. Um die richtige Anzahl der Ausnahmen segmentiert nach Ausnahmetyp abzurufen, geben Sie beispielsweise folgenden Code ein:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Die meisten wichtigen Stapelinformationen wurden bereits in separate Variablen extrahiert, Sie können jedoch die Struktur `details` analysieren, um weitere Informationen zu erhalten. Da es sich hierbei um eine dynamische Struktur handelt, muss das Ergebnis in den erwarteten Typ umgewandelt werden. Beispiel:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Verwenden Sie zum Zuordnen von Ausnahmen zu den zugehörigen Anforderungen eine Verknüpfung:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Verwenden Sie TrackTrace zum Diagnostizieren von Problemen, indem Sie eine „Brotkrümelnavigation“ an Application Insights senden. Sie können Blöcke von Diagnosedaten senden und sie in der [Diagnosesuche](./diagnostic-search.md) überprüfen.

Verwenden Sie diese API in .NET-[Protokolladaptern](./asp-net-trace-logs.md), um Drittanbieterprotokolle an das Portal zu senden.

Verwenden Sie in Java für [Standardprotokollierungen wie Log4J und Logback](./java-trace-logs.md) Application Insights-Log4J- oder Logback-Appender, um Drittanbieterprotokolle an das Portal zu senden.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*Client-/browserseitiges JavaScript*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Protokollieren eines Diagnoseereignisses, z.B. Eingeben oder Beenden einer Methode

 Parameter | BESCHREIBUNG
---|---
`message` | Diagnosedaten. Kann erheblich länger als ein Name sein.
`properties` | Zuordnen einer Zeichenfolge zu einer Zeichenfolge: Zusätzlich zum [Filtern von Ausnahmen](#properties) im Portal verwendete Daten. Ist standardmäßig leer.
`severityLevel` | Unterstützte Werte: [SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Sie können nach Nachrichteninhalt suchen, aber (anders als bei Eigenschaftswerten) nicht danach filtern.

Die Größenbeschränkung für `message` liegt wesentlich höher als der Grenzwert für Eigenschaften.
Ein Vorteil von TrackTrace ist, dass relativ lange Daten in die Nachricht eingefügt werden können. Sie können dort beispielsweise POST-Daten codieren.  

Darüber hinaus können Sie Ihrer Nachricht einen Schweregrad hinzufügen. Wie bei anderen Telemetriedaten auch können Sie Eigenschaftswerte hinzufügen, um zu filtern oder nach verschiedenen Ablaufverfolgungen zu suchen. Beispiel:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

Unter [Search](./diagnostic-search.md) können Sie dann leicht alle Nachrichten eines bestimmten Schweregrads herausfiltern, die eine bestimmte Datenbank betreffen.

### <a name="traces-in-analytics"></a>Ablaufverfolgungen in Analytics

In [Application Insights Analytics](../log-query/log-query-overview.md) werden Aufrufe von TrackTrace in der Tabelle `traces` angezeigt.

Wenn die [Stichprobenentnahme](./sampling.md) aktiv ist, wird für die itemCount-Eigenschaft ein Wert größer 1 angezeigt. „itemCount==10“ bedeutet beispielsweise, dass bei der Stichprobenentnahme von zehn `trackTrace()`-Aufrufen nur einer übertragen wurde. Zum Abrufen der richtigen Anzahl von Ablaufverfolgungsaufrufen sollten Sie daher Code wie `traces | summarize sum(itemCount)` verwenden.

## <a name="trackdependency"></a>TrackDependency

Verwenden Sie den TrackDependency-Aufruf zum Nachverfolgen der Antwortzeiten und der Erfolgsraten beim Aufrufen einer externen Codepassage. Die Ergebnisse werden in den Abhängigkeitsdiagrammen im Portal angezeigt. Der folgende Codeausschnitt muss immer dann hinzugefügt werden, wenn ein Abhängigkeitsaufruf ausgeführt wird.

> [!NOTE]
> Für .NET und .NET Core können Sie alternativ die `TelemetryClient.StartOperation`-Methode (der Erweiterung) verwenden, die die für die Korrelation benötigten `DependencyTelemetry`-Eigenschaften sowie einige weitere Eigenschaften wie die Startzeit und die Dauer auffüllt. Dadurch müssen Sie keinen benutzerdefinierten Timer wie in den folgenden Beispielen erstellen. Weitere Informationen finden Sie in diesem Artikel im [Abschnitt zur Nachverfolgung von ausgehenden Abhängigkeiten](https://docs.microsoft.com/azure/azure-monitor/app/custom-operations-tracking#outgoing-dependencies-tracking).

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    dependencyTelemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Beachten Sie, dass die Server-SDKs ein [Abhängigkeitsmodul](./asp-net-dependencies.md) enthalten, das bestimmte Abhängigkeitsaufrufe automatisch ermittelt und nachverfolgt (z.B. zu Datenbanken und REST-APIs). Sie müssen einen Agent auf dem Server installieren, damit das Modul funktioniert. 

In Java können bestimmte Abhängigkeitsaufrufe per [Java-Agent](./java-agent.md) automatisch nachverfolgt werden.

Sie verwenden diesen Aufruf, um Aufrufe nachzuverfolgen, die durch die automatisierte Nachverfolgung nicht abgefangen werden, oder wenn Sie den Agent nicht installieren möchten.

Wenn Sie das Standardmodul zum Nachverfolgen von Abhängigkeiten in C# deaktivieren möchten, bearbeiten Sie [ApplicationInsights.config](./configuration-with-applicationinsights-config.md), und löschen Sie den Verweis auf `DependencyCollector.DependencyTrackingTelemetryModule`. Installieren Sie in Java keinen Java-Agent, wenn Standardabhängigkeiten nicht automatisch gesammelt werden sollen.

### <a name="dependencies-in-analytics"></a>Abhängigkeiten in Analytics

In [Application Insights Analytics](../log-query/log-query-overview.md) werden trackDependency-Aufrufe in der Tabelle `dependencies` angezeigt.

Wenn die [Stichprobenentnahme](./sampling.md) aktiv ist, wird für die itemCount-Eigenschaft ein Wert größer 1 angezeigt. itemCount==10 bedeutet beispielsweise, dass bei der Stichprobenentnahme von 10 Aufrufen von trackDependency() nur einer übertragen wurde. Um die richtige Anzahl der Abhängigkeiten segmentiert nach Zielkomponente abzurufen, verwenden Sie beispielsweise folgenden Code:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Verwenden Sie zum Zuordnen von Abhängigkeiten zu den zugehörigen Anforderungen eine Verknüpfung:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Leeren von Daten

Normalerweise sendet das SDK Daten in festen Intervallen (in der Regel 30 Sekunden) oder wenn der Puffer voll ist (in der Regel 500 Elemente). In einigen Fällen empfiehlt es sich aber, den Puffer zu leeren – beispielsweise bei der Verwendung des SDK in einer Anwendung, die heruntergefahren wird.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Die Funktion ist für den [Servertelemetriekanal](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) asynchron.

Die flush()-Methode wird idealerweise in der Aktivität zum Herunterfahren der Anwendung verwendet.

## <a name="authenticated-users"></a>Authentifizierte Benutzer

In einer Web-App werden Benutzer (standardmäßig) [anhand von Cookies identifiziert](./usage-segmentation.md#the-users-sessions-and-events-segmentation-tool). Benutzer werden unter Umständen mehr als einmal gezählt, wenn sie über verschiedene Computer oder Browser auf Ihre App zugreifen oder Cookies löschen.

Wenn sich Benutzer bei Ihrer App anmelden, erhalten Sie einen genaueren Zählwert durch Festlegen der ID des authentifizierten Benutzers im Browsercode:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

In einer ASP.NET-MVC-Webanwendung, beispielsweise:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Es ist nicht notwendig, den tatsächlichen Anmeldenamen des Benutzers zu verwenden. Es muss sich lediglich um eine ID handeln, die für diesen Benutzer eindeutig ist. Sie darf keine Leerzeichen und keines der folgenden Zeichen enthalten: `,;=|`.

Die Benutzer-ID wird zudem in einem Sitzungscookie festgelegt und an den Server gesendet. Wenn das Server-SDK installiert ist, wird die ID des authentifizierten Benutzers als Teil der Kontexteigenschaften der Client- und Servertelemetrie gesendet. Anschließend können Sie danach filtern und suchen.

Wenn bei Ihrer App Benutzer in Konten gruppiert werden, können Sie auch einen Bezeichner für das Konto (mit den gleichen Zeicheneinschränkungen) übergeben.

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

Im [Metrik-Explorer](../platform/metrics-charts.md) können Sie ein Diagramm erstellen, das **authentifizierte Benutzer** und **Benutzerkonten** zählt.

Sie können auch nach Clientdatenpunkten mit bestimmten Benutzernamen und Konten [suchen](./diagnostic-search.md).

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtern, Durchsuchen und Segmentieren von Daten mithilfe von Eigenschaften

Sie können Ihren Ereignissen (und auch Metriken, Seitenaufrufen, Ausnahmen und anderen Telemetriedaten) Eigenschaften und Messungen anfügen.

*Eigenschaften* sind die Zeichenfolgenwerte, die Sie zum Filtern der Telemetriedaten in den Nutzungsberichten verwenden können. Wenn die Anwendung beispielsweise mehrere Spiele bereitstellt, können Sie den Namen des Spiels an jedes Ereignis anfügen, damit Sie sehen können, welche Spiele immer populärer werden.

Die Zeichenfolgenlänge ist auf 8192 Zeichen begrenzt. (Wenn Sie große Datenblöcke senden möchten, verwenden Sie den message-Parameter von TrackTrace.)

*Metriken* sind numerische Werte, die grafisch dargestellt werden können. Beispiel: Sie möchten überprüfen, ob die von den Spielern erreichten Punktzahlen stetig zunehmen. Die Diagramme können anhand der mit dem Ereignis gesendeten Eigenschaften unterteilt werden, sodass Sie für verschiedene Spiele separate oder gestapelte Diagramme erhalten.

Damit die Metrikwerte richtig angezeigt werden, sollten sie größer als oder gleich 0 sein.

Es gibt einige [Beschränkungen hinsichtlich der Anzahl von Eigenschaften, Eigenschaftswerten und Metriken](#limits) , die Sie verwenden können.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Achten Sie darauf, keine persönlich identifizierbaren Informationen in den Eigenschaften zu protokollieren.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternative Methode zum Festlegen von Eigenschaften und Metriken

Wenn es für Sie praktischer ist, können Sie die Parameter eines Ereignisses in einem separaten Objekt sammeln:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Verwenden Sie nicht dieselbe Telemetrieelementinstanz (in diesem Beispiel `event`), um „Track*()“ mehrfach aufzurufen. Dies kann dazu führen, dass Telemetriedaten mit einer falschen Konfiguration gesendet werden.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Benutzerdefinierte Messungen und Eigenschaften in Analytics

In [Analytics](../log-query/log-query-overview.md) werden benutzerdefinierte Metriken und Eigenschaften in den Attributen `customMeasurements` und `customDimensions` jedes Telemetriedatensatzes angezeigt.

Wenn Sie der Anforderungstelemetrie beispielsweise die Eigenschaft „game“ hinzugefügt haben, werden bei dieser Abfrage die Vorkommen verschiedener Werte von „game“ gezählt, und der Durchschnitt der benutzerdefinierten Metrik „score“ wird angezeigt:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Beachten Sie Folgendes:

* Wenn Sie einen Wert aus dem customDimensions- oder customMeasurements-JSON-Code extrahieren, ist dieser dynamisch typisiert. Daher müssen Sie ihn in `tostring` oder `todouble` umwandeln.
* Um die Möglichkeit der [Stichprobenentnahme](./sampling.md) zu berücksichtigen, müssen Sie `sum(itemCount)` statt `count()` verwenden.

## <a name="timing-events"></a><a name="timed"></a> Zeitmessung bei Ereignissen

In bestimmten Fällen möchten Sie im Diagramm vielleicht darstellen, wie lange es dauert, eine Aktion auszuführen. Beispielsweise möchten Sie wissen, wie lange Benutzer brauchen, um die Auswahl in einem Spiel zu erwägen. Hierfür können Sie den Messparameter verwenden.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Standardeigenschaften für benutzerdefinierte Telemetriedaten

Wenn Sie die Standardeigenschaftswerte für einige Ihrer benutzerdefinierten Ereignisse festlegen möchten, können Sie diese in einer TelemetryClient-Instanz festlegen. Sie werden jedem Telemetrieelement zugeordnet, das von diesem Client gesendet wird.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Einzelne Telemetrieaufrufe können die Standardwerte in ihren Eigenschaftenwörterbüchern überschreiben.

*Für JavaScript-Webclients* verwenden Sie JavaScript-Telemetrieinitialisierer.

Wenn Sie *allen Telemetriedaten Eigenschaften hinzufügen* möchten (einschließlich Daten aus Standardsammlungsmodulen), [implementieren Sie `ITelemetryInitializer`](./api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Stichprobenerstellung, Filterung und Verarbeitung von Telemetriedaten

Sie können Code zum Verarbeiten Ihrer Telemetriedaten schreiben, bevor diese vom SDK gesendet werden. Die Verarbeitung umfasst Daten, die von den standardmäßigen Telemetriemodulen gesendet werden, z.B. die HTTP-Anforderungsauflistung und Abhängigkeitsauflistung.

[Fügen Sie der Telemetrie Eigenschaften hinzu](./api-filtering-sampling.md#add-properties), indem Sie `ITelemetryInitializer` implementieren. Beispielsweise können Sie Versionsnummern oder Werte hinzufügen, die anhand von anderen Eigenschaften berechnet werden.

Mittels [Filterung](./api-filtering-sampling.md#filtering) können Sie Telemetriedaten ändern oder verwerfen, bevor sie vom SDK gesendet werden. Implementieren Sie hierzu `ITelemetryProcessor`. Sie steuern, was gesendet oder verworfen wird, aber Sie müssen die Auswirkung auf Ihre Metriken im Auge behalten. Je nach Vorgehensweise beim Verwerfen der Elemente kann es sein, dass Sie nicht mehr zwischen verwandten Elementen navigieren können.

[Erstellung von Stichproben](./api-filtering-sampling.md) ist eine sofort einsetzbare Methode, um das von Ihrer App an das Portal gesendete Datenvolumen zu reduzieren. Dies erfolgt ohne Auswirkung auf die angezeigten Metriken. Außerdem hat dies keinerlei Auswirkungen auf die Fähigkeit, Probleme durch Navigieren zwischen verwandten Elementen wie Ausnahmen, Anforderungen und Seitenansichten zu diagnostizieren.

[Weitere Informationen](./api-filtering-sampling.md)

## <a name="disabling-telemetry"></a>Deaktivieren der Telemetrie

So können Sie die Sammlung und Übermittlung von Telemetriedaten *dynamisch beenden und starten* :

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Um *ausgewählte Standardsammlungsmodule zu deaktivieren*, beispielsweise Leistungsindikatoren, HTTP-Anforderungen oder Abhängigkeiten, löschen Sie die entsprechenden Zeilen in [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) oder kommentieren sie aus. Diese Vorgehensweise bietet sich beispielsweise an, wenn Sie Ihre eigenen TrackRequest-Daten senden möchten.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Um *ausgewählte Standardsammlungsmodule zu deaktivieren* – beispielsweise Leistungsindikatoren, HTTP-Anforderungen oder Abhängigkeiten – verketten Sie Konfigurationsmethoden bei der Initialisierung mit Ihrem SDK-Initialisierungscode:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Verwenden Sie zum Deaktivieren dieser Sammlungsmodule nach der Initialisierung das Configuration-Objekt: `applicationInsights.Configuration.setAutoCollectRequests(false)`.

## <a name="developer-mode"></a><a name="debug"></a>Entwicklermodus

Während des Debuggens ist es sinnvoll, die Telemetriedaten beschleunigt über die Pipeline zu senden, damit die Ergebnisse sofort angezeigt werden. Sie erhalten außerdem zusätzliche Meldungen, mit denen Sie alle Probleme mit der Telemetrie verfolgen können. Schalten Sie ihn in der Produktion aus, da er Ihre App beeinträchtigen kann.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Für Node.js können Sie den Entwicklermodus aktivieren, indem Sie die interne Protokollierung über `setInternalLogging` aktivieren und `maxBatchSize` auf 0 festlegen, wodurch Ihre Telemetriedaten gesendet werden, sobald sie gesammelt wurden.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> Festlegen des Instrumentationsschlüssels für ausgewählte benutzerdefinierte Telemetriedaten

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamischer Instrumentationsschlüssel

Um das Vermischen von Telemetriedaten aus Entwicklungs-, Test- und Produktionsumgebungen zu vermeiden, können Sie [separate Application Insights-Ressourcen erstellen](./create-new-resource.md) und ihre Schlüssel abhängig von der Umgebung ändern.

Statt den Instrumentationsschlüssel aus der Konfigurationsdatei abzurufen, können Sie ihn im Code festlegen. Legen Sie den Schlüssel in einer Initialisierungsmethode fest, wie z. B. "global.aspx.cs" in einem ASP.NET-Dienst:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Auf Webseiten empfiehlt es sich, ihn über den Zustand des Webservers festzulegen, anstatt ihn im Skript zu codieren. Beispielsweise auf einer Webseite, die in einer ASP.NET-App generiert wird:

*JavaScript in Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient besitzt eine Context-Eigenschaft mit Werten, die zusammen mit allen Telemetriedaten gesendet werden. Sie werden normalerweise von den Standardtelemetriemodulen festgelegt, aber Sie können sie auch selbst einstellen. Beispiel:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Wenn Sie diese Werte selbst festlegen, empfiehlt es sich, die entsprechende Zeile aus [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) zu entfernen, damit kein Konflikt zwischen Ihren Werten und den Standardwerten entsteht.

* **Component:** Die App und ihre Version.
* **Device:** Daten zu dem Gerät, auf dem die App ausgeführt wird. (In Web-Apps ist dies das Server- oder Clientgerät, von dem die Telemetriedaten gesendet werden.)
* **InstrumentationKey:** Die Application Insights-Ressource in Azure, in der die Telemetriedaten angezeigt werden. In der Regel wird diese aus der Datei „ApplicationInsights.config“ übernommen.
* **Standort**: Der geografische Standort des Geräts.
* **Operation:** In Web-Apps die aktuelle HTTP-Anforderung. In anderen App-Typen können Sie dies zur Gruppierung von Ereignissen festlegen.
  * **ID:** Ein generierter Wert, der verschiedene Ereignisse korreliert, sodass Sie beim Untersuchen eines Ereignisses in der Diagnosesuche verwandte Elemente finden können.
  * **Name**: Ein Bezeichner, in der Regel die URL der HTTP-Anforderung.
  * **SyntheticSource:** Wenn diese Zeichenfolge nicht NULL oder leer ist, wird damit angegeben, dass die Quelle der Anforderung als Roboter oder Webtest identifiziert wurde. Standardmäßig wird sie von Berechnungen im Metrik-Explorer ausgeschlossen.
* **Properties:** Eigenschaften, die mit allen Telemetriedaten gesendet werden. Kann in einzelnen Track*-Aufrufen außer Kraft gesetzt werden.
* **Sitzung (Session)** : Die Sitzung des Benutzers. Die ID wird auf einen generierten Wert festgelegt, der geändert wird, wenn der Benutzer für eine Weile nicht aktiv ist.
* **Benutzer:** Benutzerinformationen.

## <a name="limits"></a>Einschränkungen

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Nutzen Sie die [Stichprobenerstellung (Sampling)](./sampling.md), um zu vermeiden, dass Sie die Datenratengrenze erreichen.

Informationen dazu, wie lange Daten aufbewahrt werden, finden Sie unter [Datenspeicherung und Datenschutz](./data-retention-privacy.md).

## <a name="reference-docs"></a>Referenz

* [ASP.NET-Referenz](/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Java-Referenz](/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [JavaScript-Referenz](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## <a name="sdk-code"></a>SDK-Code

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-Pakete](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="questions"></a>Fragen

* *Welche Ausnahmen werden möglicherweise von Aufrufen vom Typ „Track_()“ ausgelöst?*

    Keine. Sie müssen sie nicht mit try/catch-Klauseln umschließen. Wenn beim SDK Probleme auftreten, werden Meldungen in der Debugkonsolenausgabe und – sofern die Meldungen ankommen – in der Diagnosesuche protokolliert.
* *Gibt es eine REST-API zum Abrufen von Daten aus dem Portal?*

    Ja, die [Datenzugriffs-API](https://dev.applicationinsights.io/). Weitere Methoden zum Extrahieren von Daten stellen das [Exportieren aus Analytics in Power BI](./export-power-bi.md) und der [fortlaufende Export](./export-telemetry.md) dar.

## <a name="next-steps"></a><a name="next"></a>Nächste Schritte

* [Durchsuchen von Ereignissen und Protokollen](./diagnostic-search.md)
* [Problembehandlung](../faq.md)

