---
title: Azure Application Insights-Datenmodell | Microsoft-Dokumentation
description: Beschreibt die Eigenschaften, die aus dem fortlaufenden Export in JSON exportiert und als Filter verwendet wurden.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: mbullwin
ms.openlocfilehash: 4f8fd0b317c17f142664d22291c23442dd49f970
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053291"
---
# <a name="application-insights-export-data-model"></a>Application Insights-Exportdatenmodell
In dieser Tabelle sind die Eigenschaften der Telemetriedaten aufgelistet, die von [Application Insights](../../azure-monitor/app/app-insights-overview.md) SDKs an das Portal gesendet werden.
Sie sehen diese Eigenschaften in der Datenausgabe von [Fortlaufender Export](export-telemetry.md).
Sie werden darüber hinaus in den Eigenschaftsfiltern im [Metrik-Explorer](../../azure-monitor/app/metrics-explorer.md) und in der [Diagnosesuche](../../azure-monitor/app/diagnostic-search.md) angezeigt.

Beachten Sie Folgendes:

* `[0]` in diesen Tabellen bezeichnet Punkte im Pfad, an denen Sie einen Index einfügen müssen; es handelt sich jedoch nicht immer um 0.
* Zeitspannen werden als Zehntel einer Mikrosekunde angegeben, also 10000000 == 1 Sekunde.
* Datums- und Zeitangaben sind als UTC und im ISO-Format angegeben: `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Beispiel
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Kontext
Alle Telemetriedatentypen umfassen einen Kontextabschnitt. Nicht alle dieser Felder werden mit jedem Datenpunkt übertragen.

| `Path` | Type | Notizen |
| --- | --- | --- |
| context.custom.dimensions [0] |object [ ] |Schlüssel-Wert-Paare, die über benutzerdefinierte Eigenschaftenparameter festgelegt werden. Maximale Länge des Schlüssels: 100, maximale Länge des Werts: 1024. Mehr als 100 eindeutige Werte, die Eigenschaft kann durchsucht, aber nicht zur Segmentierung verwendet werden. Maximal 200 Schlüssel pro „ikey“. |
| context.custom.metrics [0] |object [ ] |Schlüssel-Wert-Paare, die über benutzerdefinierte Messparameter und über TrackMetrics festgelegt werden. Maximale Länge des Schlüssels: 100, Werte können numerisch sein. |
| context.data.eventTime |Zeichenfolge |UTC |
| context.data.isSynthetic |boolean |Anforderung scheint von einem Bot oder Webtest zu stammen. |
| context.data.samplingRate |number |Prozentsatz der vom SDK generierten Telemetriedaten, die an das Portal gesendet werden. Bereich liegt zwischen 0.0 und 100.0. |
| context.device |object |Clientgerät |
| context.device.browser |Zeichenfolge |IE, Chrome, ... |
| context.device.browserVersion |Zeichenfolge |Chrome 48.0, ... |
| context.device.deviceModel |Zeichenfolge | |
| context.device.deviceName |Zeichenfolge | |
| context.device.id |Zeichenfolge | |
| context.device.locale |Zeichenfolge |en-GB, de-DE, ... |
| context.device.network |Zeichenfolge | |
| context.device.oemName |Zeichenfolge | |
| context.device.os |Zeichenfolge | |
| context.device.osVersion |Zeichenfolge |Hostbetriebssystem |
| context.device.roleInstance |Zeichenfolge |ID des Serverhosts |
| context.device.roleName |Zeichenfolge | |
| context.device.screenResolution |Zeichenfolge | |
| context.device.type |Zeichenfolge |PC, Browser, ... |
| context.location |object |Abgeleitet von „clientip“. |
| context.location.city |Zeichenfolge |Abgeleitet von „clientip“, falls bekannt |
| context.location.clientip |Zeichenfolge |Letztes Oktagon wird als 0 anonymisiert. |
| context.location.continent |Zeichenfolge | |
| context.location.country |Zeichenfolge | |
| context.location.province |Zeichenfolge |Bundesland oder Kanton |
| context.operation.id |Zeichenfolge |Elemente mit derselben Vorgangs-ID werden im Portal als verwandte Elemente angezeigt. Üblicherweise die Anforderungs-ID. |
| context.operation.name |Zeichenfolge |URL oder Anforderungsname |
| context.operation.parentId |Zeichenfolge |Ermöglicht geschachtelte verwandte Elemente. |
| context.session.id |Zeichenfolge |ID einer Gruppe von Vorgängen derselben Quelle. Ein Zeitraum von 30 Minuten ohne Vorgang signalisiert das Ende einer Sitzung. |
| context.session.isFirst |boolean | |
| context.user.accountAcquisitionDate |Zeichenfolge | |
| context.user.accountId |Zeichenfolge | |
| context.user.anonAcquisitionDate |Zeichenfolge | |
| context.user.anonId |Zeichenfolge | |
| context.user.authAcquisitionDate |Zeichenfolge |[Authentifizierter Benutzer](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |Zeichenfolge | |
| context.user.isAuthenticated |boolean | |
| context.user.storeRegion |Zeichenfolge | |
| internal.data.documentVersion |Zeichenfolge | |
| internal.data.id |Zeichenfolge | Eindeutige ID, die beim Erfassen eines Elements in Application Insights zugewiesen wird. |

## <a name="events"></a>Events
Von [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)generierte benutzerdefinierte Ereignisse.

| `Path` | Type | Notizen |
| --- | --- | --- |
| event [0] count |integer |100/([Stichproben](../../azure-monitor/app/sampling.md) -Prozentsatz). Beispiel: 4 =&gt; 25 %. |
| event [0] name |Zeichenfolge |Ereignisname.  Länge: 250 |
| event [0] url |Zeichenfolge | |
| event [0] urlData.base |Zeichenfolge | |
| event [0] urlData.host |Zeichenfolge | |

## <a name="exceptions"></a>Ausnahmen
Melden [Ausnahmen](../../azure-monitor/app/asp-net-exceptions.md) auf dem Server und im Browser.

| `Path` | Type | Notizen |
| --- | --- | --- |
| basicException [0] assembly |Zeichenfolge | |
| basicException [0] count |integer |100/([Stichproben](../../azure-monitor/app/sampling.md) -Prozentsatz). Beispiel: 4 =&gt; 25 %. |
| basicException [0] exceptionGroup |Zeichenfolge | |
| basicException [0] exceptionType |Zeichenfolge | |
| basicException [0] failedUserCodeMethod |Zeichenfolge | |
| basicException [0] failedUserCodeAssembly |Zeichenfolge | |
| basicException [0] handledAt |Zeichenfolge | |
| basicException [0] hasFullStack |boolean | |
| basicException [0] id |Zeichenfolge | |
| basicException [0] method |Zeichenfolge | |
| basicException [0] message |Zeichenfolge |Ausnahmemeldung. Max. Länge: 10.000 |
| basicException [0] outerExceptionMessage |Zeichenfolge | |
| basicException [0] outerExceptionThrownAtAssembly |Zeichenfolge | |
| basicException [0] outerExceptionThrownAtMethod |Zeichenfolge | |
| basicException [0] outerExceptionType |Zeichenfolge | |
| basicException [0] outerId |Zeichenfolge | |
| basicException [0] parsedStack [0] assembly |Zeichenfolge | |
| basicException [0] parsedStack [0] fileName |Zeichenfolge | |
| basicException [0] parsedStack [0] level |integer | |
| basicException [0] parsedStack [0] line |integer | |
| basicException [0] parsedStack [0] method |Zeichenfolge | |
| basicException [0] stack |Zeichenfolge |Max. Länge: 10.000 |
| basicException [0] typeName |Zeichenfolge | |

## <a name="trace-messages"></a>Ablaufverfolgungsmeldungen
Gesendet von [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) und über die [Protokollierungsadapter](../../azure-monitor/app/asp-net-trace-logs.md).

| `Path` | Type | Notizen |
| --- | --- | --- |
| message [0] loggerName |Zeichenfolge | |
| message [0] parameters |Zeichenfolge | |
| message [0] raw |Zeichenfolge |Die Protokollmeldung, maximale Länge: 10.000. |
| message [0] severityLevel |Zeichenfolge | |

## <a name="remote-dependency"></a>Remoteabhängigkeit
Gesendet von TrackDependency. Wird zum Berichten von Leistung und Nutzung von [Aufrufen von abhängigen Komponenten](../../azure-monitor/app/asp-net-dependencies.md) auf dem Server sowie von AJAX-Aufrufen im Browser verwendet.

| `Path` | Type | Notizen |
| --- | --- | --- |
| remoteDependency [0] async |boolean | |
| remoteDependency [0] baseName |Zeichenfolge | |
| remoteDependency [0] commandName |Zeichenfolge |Beispiel: "home/index" |
| remoteDependency [0] count |integer |100/([Stichproben](../../azure-monitor/app/sampling.md) -Prozentsatz). Beispiel: 4 =&gt; 25 %. |
| remoteDependency [0] dependencyTypeName |Zeichenfolge |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |number |Zeit vom Aufruf bis zum Abschließen der Antwort durch die Abhängigkeit. |
| remoteDependency [0] id |Zeichenfolge | |
| remoteDependency [0] name |Zeichenfolge |URL, Max. Länge: 250 |
| remoteDependency [0] resultCode |Zeichenfolge |Aus HTTP-Abhängigkeit |
| remoteDependency [0] success |boolean | |
| remoteDependency [0] type |Zeichenfolge |HTTP, SQL, ... |
| remoteDependency [0] url |Zeichenfolge |Max. Länge: 2000 |
| remoteDependency [0] urlData.base |Zeichenfolge |Max. Länge: 2000 |
| remoteDependency [0] urlData.hashTag |Zeichenfolge | |
| remoteDependency [0] urlData.host |Zeichenfolge |Max. Länge: 200 |

## <a name="requests"></a>Requests
Gesendet von [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Die Standardmodule verwenden TrackRequest zum Berichten der Serverantwortzeit, gemessen auf dem Server.

| `Path` | Type | Notizen |
| --- | --- | --- |
| request [0] count |integer |100/([Stichproben](../../azure-monitor/app/sampling.md) -Prozentsatz). Beispiel:  4 =&gt; 25 %. |
| request [0] durationMetric.value |number |Zeit vom Empfang der Anforderung bis zur Antwort. 1e7 == 1s |
| request [0] id |Zeichenfolge |Vorgangs-ID |
| request [0] name |Zeichenfolge |GET/POST + URL-Basis.  Max. Länge: 250 |
| request [0] responseCode |integer |HTTP-Antwort, die an den Client gesendet wird. |
| request [0] success |boolean |Standard == (responseCode &lt; 400) |
| request [0] url |Zeichenfolge |Host nicht eingeschlossen |
| request [0] urlData.base |Zeichenfolge | |
| request [0] urlData.hashTag |Zeichenfolge | |
| request [0] urlData.host |Zeichenfolge | |

## <a name="page-view-performance"></a>Leistung der Seitenaufrufe
Gesendet vom Browser. Misst die Zeit zum Verarbeiten einer Seite, von der Initialisierung der Anforderung durch den Benutzer bis zur vollständigen Anzeige (asynchrone AJAX-Aufrufe ausgenommen).

Kontextwerte zeigen Clientbetriebssystem und Browserversion.

| `Path` | Type | Notizen |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |integer |Zeit vom vollständigen Empfang der HTML-Daten bis zur Anzeige der Seite. |
| clientPerformance [0] name |Zeichenfolge | |
| clientPerformance [0] networkConnection.value |integer |Benötigte Zeit zum Herstellen einer Netzwerkverbindung. |
| clientPerformance [0] receiveRequest.value |integer |Zeit vom vollständigen Senden der Anforderung bis zum Empfang der HTML-Daten in der Antwort. |
| clientPerformance [0] sendRequest.value |integer |Benötigte Zeit zum Senden der HTTP-Anforderung. |
| clientPerformance [0] total.value |integer |Zeit vom Sendebeginn der Anforderung bis zur Anzeige der Seite. |
| clientPerformance [0] url |Zeichenfolge |URL dieser Anforderung. |
| clientPerformance [0] urlData.base |Zeichenfolge | |
| clientPerformance [0] urlData.hashTag |Zeichenfolge | |
| clientPerformance [0] urlData.host |Zeichenfolge | |
| clientPerformance [0] urlData.protocol |Zeichenfolge | |

## <a name="page-views"></a>Seitenaufrufe
Gesendet von trackPageView() oder [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| `Path` | Type | Notizen |
| --- | --- | --- |
| view [0] count |integer |100/([Stichproben](../../azure-monitor/app/sampling.md) -Prozentsatz). Beispiel: 4 =&gt; 25 %. |
| view [0] durationMetric.value |integer |Wert optional in trackPageView() oder über startTrackPage() - stopTrackPage() festgelegt. Ist nicht dasselbe wie die clientPerformance-Werte. |
| view [0] name |Zeichenfolge |Seitentitel, Max.  Länge: 250 |
| view [0] url |Zeichenfolge | |
| view [0] urlData.base |Zeichenfolge | |
| view [0] urlData.hashTag |Zeichenfolge | |
| view [0] urlData.host |Zeichenfolge | |

## <a name="availability"></a>Verfügbarkeit
Liefert Berichtdaten zu [Verfügbarkeitswebtests](../../azure-monitor/app/monitor-web-app-availability.md).

| `Path` | Type | Notizen |
| --- | --- | --- |
| availability [0] availabilityMetric.name |Zeichenfolge |Verfügbarkeit |
| availability [0] availabilityMetric.value |number |1.0 oder 0.0 |
| availability [0] count |integer |100/([Stichproben](../../azure-monitor/app/sampling.md) -Prozentsatz). Beispiel: 4 =&gt; 25 %. |
| availability [0] dataSizeMetric.name |Zeichenfolge | |
| availability [0] dataSizeMetric.value |integer | |
| availability [0] durationMetric.name |Zeichenfolge | |
| availability [0] durationMetric.value |number |Dauer des Tests. 1e7==1s |
| availability [0] message |Zeichenfolge |Fehlerdiagnose |
| availability [0] result |Zeichenfolge |Erfolgreich/Fehler |
| availability [0] runLocation |Zeichenfolge |Geoquelle der HTTP-Anforderung. |
| availability [0] testName |Zeichenfolge | |
| availability [0] testRunId |Zeichenfolge | |
| availability [0] testTimestamp |Zeichenfolge | |

## <a name="metrics"></a>metrics
Generiert von TrackMetric().

Der Metrikwert befindet sich in context.custom.metrics[0].

Beispiel:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Grundlegendes zu Metrikwerten
Informationen zu Metrikwerten werden, sowohl in Metrikberichten als auch an anderer Stelle, mit einer Standardobjektstruktur bereitgestellt. Beispiel:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Derzeit – dies kann sich in Zukunft ändern – sind in allen Werten, die von den SDK-Standardmodulen berichtet werden, `count==1` und nur die Felder `name` und `value` von Interesse. Dies wäre nur anders, wenn Sie eigene TrackMetric-Aufrufe schreiben, in denen Sie die weiteren Parameter festlegen.

Der Zweck der weiteren Felder besteht darin, Metriken im SDK zu aggregieren, um den Datenverkehr im Portal zu verringern. Sie könnten beispielsweise den Durchschnitt für mehrere aufeinanderfolgende Lesevorgänge ermitteln, bevor jeder Metrikbericht gesendet wird. Anschließend könnten Sie die Mindest-, Maximal- und Standardabweichung und den Aggregatwert (Summe oder Durchschnitt) berechnen und die Anzahl auf die Anzahl von Lesevorgängen festlegen, die der Bericht repräsentiert

In den obigen Tabellen wurden die selten verwendeten Felder „count“, „min“, „max“, „stdDev“ und „sampledValue“ ausgelassen.

Anstelle einer Vorabaggretation von Metriken können Sie die [Stichprobenerstellung](../../azure-monitor/app/sampling.md) nutzen, wenn Sie den Umfang der Telemetriedaten verringern möchten.

### <a name="durations"></a>Zeitspannen
Sofern nicht anders angegeben, wird die Dauer in Zehnteln einer Mikrosekunde angegeben, d. h. 10000000.0 entspricht 1 Sekunde.

## <a name="see-also"></a>Weitere Informationen
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Fortlaufender Export](export-telemetry.md)
* [Codebeispiele](export-telemetry.md#code-samples)
