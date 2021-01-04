---
title: Azure Application Insights – Protokollbasierte Metriken | Microsoft-Dokumentation
description: Dieser Artikel listet die Metriken von Azure Application Insights mit unterstützten Aggregationen und Dimensionen auf. Zu den Details der protokollbasierten Metriken gehören die zugrunde liegenden Kusto-Abfrageanweisungen.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 5c61287475eb82241aa5c9e1d1649e8b20e3b28c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185945"
---
# <a name="application-insights-log-based-metrics"></a>Protokollbasierte Metriken von Application Insights

Mit den protokollbasierten Metriken von Application Insights können Sie die Integrität Ihrer überwachten Apps analysieren, leistungsstarke Dashboards erstellen und Warnungen konfigurieren. Es gibt zwei Arten von Metriken:

* [Protokollbasierte Metriken](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) im Hintergrund werden aus gespeicherten Ereignissen in [Kusto-Abfragen](/azure/kusto/query/) übersetzt.
* [Standardmetriken](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) werden als vorab aggregierte Zeitreihe gespeichert.

Da *Standardmetriken* während der Erfassung vorab aggregiert werden, weisen sie zum Abfragezeitpunkt eine bessere Leistung auf. Dies macht sie zu einer besseren Wahl für Dashboarding und Echtzeitwarnungen. Die *protokollbasierten Metriken* verfügen über mehr Dimensionen, was sie zur überlegenen Option für die Datenanalyse und Ad-hoc-Diagnosen macht. Verwenden Sie die [Namespace-Auswahl](metrics-getting-started.md#create-your-first-metric-chart), um im [Metrik-Explorer](metrics-getting-started.md) zwischen protokollbasierten Metriken und Standardmetriken zu wechseln.

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretieren und Verwenden von Abfragen aus diesem Artikel

Dieser Artikel listet Metriken mit unterstützten Aggregationen und Dimensionen auf. Zu den Details der protokollbasierten Metriken gehören die zugrunde liegenden Kusto-Abfrageanweisungen. Zur Vereinfachung verwendet jede Abfrage Standardwerte für Zeitgranularität, Diagrammtyp und manchmal auch Teilungsdimensionen, was die Verwendung der Abfrage in Log Analytics vereinfacht, ohne dass Änderungen erforderlich sind.

Wenn Sie die gleiche Metrik im [Metrik-Explorer](metrics-getting-started.md) darstellen, gibt es keine Standardwerte – die Abfrage wird dynamisch entsprechend Ihren Diagrammeinstellungen angepasst:

- Der ausgewählte **Zeitbereich** wird in eine zusätzliche *where timestamp*-Klausel übersetzt, um nur die Ereignisse aus dem ausgewählten Zeitbereich auszuwählen. Beispielsweise enthält ein Diagramm, in dem Daten der letzten 24 Stunden angezeigt werden, die Abfrage *| where timestamp > ago(24 h)* .

- Die ausgewählte **Zeitgranularität** wird in die endgültige *summarize ... by bin(timestamp, [time grain])* -Klausel aufgenommen.

- Alle ausgewählten **Filter**-Dimensionen werden in zusätzliche *where*-Klauseln übersetzt.

- Die ausgewählte **Teilungsdiagramm**-Dimension wird in eine zusätzliche Zusammenfassungseigenschaft übersetzt. Wenn Sie beispielsweise Ihr Diagramm nach *Standort* aufteilen und mit einer Zeitgranularität von 5 Minuten grafisch darstellen, wird die *summarize*-Klausel in der Form *... by bin(timestamp, 5 m), location* zusammengefasst.

> [!NOTE]
> Wenn Sie noch nicht mit der Kusto-Abfragesprache vertraut sind, müssen Sie zunächst die Kusto-Anweisungen kopieren und in den Log Analytics-Abfragebereich einfügen, ohne Änderungen vorzunehmen. Klicken Sie auf **Ausführen**, um das grundlegende Diagramm anzuzeigen. Wenn Sie mit der Syntax der Abfragesprache vertraut sind, können Sie damit beginnen, kleine Änderungen vorzunehmen und die Auswirkung Ihrer Änderung anzuzeigen. Das Untersuchen Ihrer eigenen Daten ist eine gute Möglichkeit, um die volle Leistungsfähigkeit von [Log Analytics](../log-query/log-analytics-tutorial.md) und [Azure Monitor](../overview.md) zu erkennen.

## <a name="availability-metrics"></a>Verfügbarkeitsmetriken

Metriken der Kategorie „Verfügbarkeit“ ermöglichen es Ihnen, die Integrität Ihrer Webanwendung so anzuzeigen, wie sie an Punkten auf der ganzen Welt zu beobachten ist. [Konfigurieren Sie die Verfügbarkeitstests](../app/monitor-web-app-availability.md), damit Sie Metriken aus dieser Kategorie verwenden können.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Verfügbarkeit (availabilityResults/availabilityPercentage)
Die Metrik *Verfügbarkeit* zeigt den Prozentsatz der Webtestläufe, bei denen keine Probleme erkannt wurden. Der kleinstmögliche Wert ist 0 und gibt an, dass bei allen Webtestläufen Fehler aufgetreten sind. Der Wert 100 bedeutet, dass alle Webtestläufe die Überprüfungskriterien erfüllt haben.

|Unit of measure|Unterstützte Aggregationen|Unterstützte Dimensionen|
|---|---|---|---|---|---|
|Prozentwert|Average|Ausführungsort, Testname|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Verfügbarkeitstestdauer (availabilityResults/duration)

Die Metrik *Verfügbarkeitstestdauer* gibt an, wie lange die Ausführung des Webtests gedauert hat. Bei den [mehrstufigen Webtests](../app/availability-multistep.md) spiegelt die Metrik die gesamte Ausführungszeit aller Stufen wider.

|Unit of measure|Unterstützte Aggregationen|Unterstützte Dimensionen|
|---|---|---|---|---|---|
|Millisekunden|Durchschnitt, Minimum, Maximum|Ausführungsort, Testname, Testergebnis

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Verfügbarkeitstests (availabilityResults/count)

Die Metrik *Verfügbarkeitstests* spiegelt die Anzahl der von Azure Monitor ausgeführten Webtests wider.

|Unit of measure|Unterstützte Aggregationen|Unterstützte Dimensionen|
|---|---|---|---|---|---|
|Anzahl|Anzahl|Ausführungsort, Testname, Testergebnis|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Browsermetriken

Browsermetriken werden vom Application Insights JavaScript SDK aus echten Endbenutzerbrowsern gesammelt. Sie bieten nützliche Einblicke in die Erfahrungen der Benutzer mit Ihrer Webanwendung. Für Browsermetriken werden in der Regel keine Stichproben verwendet, was bedeutet, dass sie eine höhere Genauigkeit der Nutzungszahlen bieten als serverseitige Metriken, die durch die Verwendung von Stichproben verzerrt sein können.

> [!NOTE]
> Um Browsermetriken zu erfassen, muss Ihre Anwendung mit dem [Application Insights JavaScript SDK](../app/javascript.md) ausgestattet sein.

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Browser-Seitenladezeit (browserTimings/totalDuration)

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|
|---|---|---|
|Millisekunden|Durchschnitt, Minimum, Maximum|Keine|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Clientverarbeitungszeit (browserTiming/processingDuration)

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|
|---|---|---|
|Millisekunden|Durchschnitt, Minimum, Maximum|Keine|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Netzwerkverbindungszeit zum Laden der Seite (browserTimings/networkDuration)

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|
|---|---|---|
|Millisekunden|Durchschnitt, Minimum, Maximum|Keine|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Empfängt Antwortzeit (browserTimings/receiveDuration)

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|
|---|---|---|
|Millisekunden|Durchschnitt, Minimum, Maximum|Keine|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Anforderungszeit senden (browserTimings/sendDuration)

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|
|---|---|---|
|Millisekunden|Durchschnitt, Minimum, Maximum|Keine|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Fehlermetriken

Die Metriken der Kategorie **Fehler** zeigen Probleme bei der Verarbeitung von Anforderungen, Abhängigkeitsaufrufen und ausgelösten Ausnahmen.

### <a name="browser-exceptions-exceptionsbrowser"></a>Browserausnahmen (exceptions/browser)

Diese Metrik spiegelt die Anzahl der ausgelösten Ausnahmen von Ihrem Anwendungscode im Browser wider. Nur Ausnahmen, die mit einem ```trackException()``` Application Insights API-Aufruf verfolgt werden, sind in der Metrik enthalten.

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|Notizen|
|---|---|---|---|
|Anzahl|Anzahl|Keine|Protokollbasierte Version verwendet **Sum**-Aggregation|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Fehler bei Abhängigkeitsaufrufen (dependencies/failed)

Die Anzahl fehlerhafter Abhängigkeitsaufrufe.

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|Notizen|
|---|---|---|---|
|Anzahl|Anzahl|Keine|Protokollbasierte Version verwendet **Sum**-Aggregation|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Ausnahmen (exceptions/count)

Jedes Mal, wenn Sie eine Ausnahme bei Application Insights protokollieren, erfolgt ein Aufruf der [trackException()-Methode](../app/api-custom-events-metrics.md#trackexception) des SDK. Die Metrik „Ausnahmen“ zeigt die Anzahl der protokollierten Ausnahmen an.

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|Notizen|
|---|---|---|---|
|Anzahl|Anzahl|Cloudrollenname, Cloudrolleninstanz, Gerätetyp|Protokollbasierte Version verwendet **Sum**-Aggregation|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Fehlerhafte Anforderungen (requests/failed)

Die Anzahl der verfolgten Serveranforderungen, die als *fehlgeschlagen* markiert wurden. Standardmäßig markiert das Application Insights SDK automatisch jede Serveranforderung, die den HTTP-Antwortcode 5xx oder 4xx zurückgegeben hat, als fehlerhafte Anforderung. Sie können diese Logik anpassen, indem Sie die Eigenschaft *Erfolg* des Anforderungstelemetrieelements in einem [benutzerdefinierten Telemetrieinitialisierer](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) ändern.

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|Notizen|
|---|---|---|---|
|Anzahl|Anzahl|Cloudrolleninstanz, Cloudrollenname, Echter oder synthetischer Datenverkehr, Anforderungsleistung, Antwortcode|Protokollbasierte Version verwendet **Sum**-Aggregation|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Serverausnahmen (exceptions/server)

Diese Metrik zeigt die Anzahl der Serverausnahmen.

|Unit of measure|Unterstützte Aggregationen|Vorab aggregierte Dimensionen|Notizen|
|---|---|---|---|
|Anzahl|Anzahl|Cloudrollenname, Cloudrolleninstanz|Protokollbasierte Version verwendet **Sum**-Aggregation|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Leistungsindikatoren

Verwenden Sie Metriken in der Kategorie **Leistungsindikatoren**, um auf [die von Application Insights erfassten Systemleistungsindikatoren](../app/performance-counters.md) zuzugreifen.

### <a name="available-memory-performancecountersavailablememory"></a>Verfügbarer Speicher (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Ausnahmerate (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Ausführungszeit der HTTP-Anforderung (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-Anforderungsrate (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-Anforderungen in der Anwendungswarteschlange (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Prozess-CPU (performanceCounters/processCpuPercentage)

Die Metrik zeigt, wie viel der gesamten Prozessorleistung von dem Prozess genutzt wird, der Ihre überwachte App hostet.

|Unit of measure|Unterstützte Aggregationen|Unterstützte Dimensionen|
|---|---|---|
|Prozentwert|Durchschnitt, Minimum, Maximum|Cloudrolleninstanz

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>E/A-Rate des Prozesses (performanceCounters/processIOBytesPerSecond)

|Unit of measure|Unterstützte Aggregationen|Unterstützte Dimensionen|
|---|---|---|
|Bytes pro Sekunde|Durchschnitt, Minimum, Maximum|Cloudrolleninstanz

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Private Bytes des Prozesses (performanceCounters/processPrivateBytes)

Menge des nicht gemeinsam genutzten Arbeitsspeichers, die der überwachte Prozess für seine Daten reserviert hat.

|Unit of measure|Unterstützte Aggregationen|Unterstützte Dimensionen|
|---|---|---|
|Byte|Durchschnitt, Minimum, Maximum|Cloudrolleninstanz

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Prozessorzeit (performanceCounters/processorCpuPercentage)

CPU-Auslastung durch *alle* Prozesse, die auf der überwachten Serverinstanz ausgeführt werden.

|Unit of measure|Unterstützte Aggregationen|Unterstützte Dimensionen|
|---|---|---|
|Prozentwert|Durchschnitt, Minimum, Maximum|Cloudrolleninstanz

>[!NOTE]
> Die Prozessorzeitmetrik ist für die in Azure App Services gehosteten Anwendungen nicht verfügbar. Verwenden Sie die [Prozess-CPU](#process-cpu-performancecountersprocesscpupercentage)-Metrik, um die CPU-Auslastung der in App Services gehosteten Webanwendungen zu verfolgen.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Servermetriken

### <a name="dependency-calls-dependenciescount"></a>Abhängigkeitsaufrufe (dependencies/count)

Diese Metrik bezieht sich auf die Anzahl der Abhängigkeitsaufrufe.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Dauer der Abhängigkeit (dependencies/duration)

Diese Metrik bezieht sich auf die Dauer von Abhängigkeitsaufrufen.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Serveranforderungen (requests/count)

Diese Metrik spiegelt die Anzahl der eingehenden Serveranforderungen wider, die von Ihrer Webanwendung empfangen wurden.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Serverantwortzeit (requests/duration)

Diese Metrik spiegelt die Zeit wider, die die Server für die Verarbeitung eingehender Anforderungen benötigt haben.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Nutzungsmetriken

### <a name="page-view-load-time-pageviewsduration"></a>Ladezeit der Seitenansicht (pageViews/duration)

Diese Metrik bezieht sich auf die Zeit, die für das Laden von PageView-Ereignissen benötigt wurde.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Seitenaufrufe (pageViews/count)

Die Anzahl der PageView-Ereignisse, die mit der TrackPageView() Application Insights API protokolliert wurden.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sitzungen (sessions/count)

Diese Metrik bezieht sich auf die Anzahl der unterschiedlichen Sitzungs-IDs.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Überwachungen (traces/count)

Die Anzahl der mit dem TrackTrace() Application Insights API-Aufruf protokollierten Überwachungsanweisungen.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Benutzer (users/count)

Die Anzahl der unterschiedlichen Benutzer, die auf Ihre Anwendung zugegriffen haben. Die Genauigkeit dieser Metrik kann durch die Verwendung von Telemetriestichproben und Filtern erheblich beeinträchtigt werden.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Benutzer, Authentifiziert (users/authenticated)

Die Anzahl der unterschiedlichen Benutzer, die sich bei Ihrer Anwendung authentifiziert haben.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```