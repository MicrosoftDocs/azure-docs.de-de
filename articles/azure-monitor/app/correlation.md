---
title: Korrelation der Azure Application Insights-Telemetrie | Microsoft-Dokumentation
description: Korrelation der Application Insights-Telemetrie
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 7a206d61124b2524feae0cf155083c1939696e18
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853321"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetriekorrelation in Application Insights

In der Welt der Mikroservices müssen für jeden logischen Vorgang Aufgaben mit den verschiedenen Dienstkomponenten durchgeführt werden. Sie können jede dieser Komponenten separat mit [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) überwachen. Application Insights unterstützt verteilte Telemetriekorrelation, mit der Sie erkennen können, welche Komponente für Fehler oder Leistungsbeeinträchtigungen verantwortlich ist.

In diesem Artikel wird das von Application Insights verwendete Datenmodell erläutert, mit dem die von mehreren Komponenten gesendeten Telemetriedaten korreliert werden. Es werden Techniken und Protokolle zur Kontextpropagierung erläutert. Darüber hinaus wird auch die Implementierung von Korrelationstaktiken in verschiedenen Sprachen und auf verschiedenen Plattformen behandelt.

## <a name="data-model-for-telemetry-correlation"></a>Datenmodell für Telemetriekorrelation

Application Insights definiert ein [Datenmodell](../../azure-monitor/app/data-model.md) für die verteilte Telemetriekorrelation. Um die Telemetrie mit einem logischen Vorgang zu verknüpfen, ist jedes Telemetrieelement mit einem Kontextfeld namens `operation_Id` versehen. Dieser Bezeichner wird von jedem Telemetrieelement in der verteilten Ablaufverfolgung gemeinsam genutzt. Selbst wenn Ihnen Telemetriedaten von einer einzelnen Ebene verloren gehen, können Sie weiterhin Telemetriedaten zuordnen, die von anderen Komponenten gemeldet wurden.

Der verteilte logische Vorgang besteht in der Regel aus einem Satz von kleineren Vorgängen, nämlich den Anforderungen, die von einer der Komponenten verarbeitet werden. Diese Vorgänge werden von einer [Anforderungstelemetrie](../../azure-monitor/app/data-model-request-telemetry.md) definiert. Jedes Anforderungstelemetrieelement verfügt über eine eigene `id`, die es eindeutig und global identifiziert. Für sämtliche Telemetrieelemente (z.B. Ablaufverfolgungen und Ausnahmen), die der Anforderung zugeordnet sind, sollte die `operation_parentId` auf den Wert der Anforderungs-`id` festgelegt werden.

Alle ausgehenden Vorgänge wie HTTP-Aufrufe an andere Komponenten werden durch eine [Abhängigkeitstelemetrie](../../azure-monitor/app/data-model-dependency-telemetry.md) dargestellt. Abhängigkeitstelemetrien definieren zudem ihre eigene `id`, die global eindeutig ist. Anforderungsabhängigkeiten, die durch diese Anforderungstelemetrie initiiert werden, verwenden diese `id` als `operation_parentId`.

Sie können eine Ansicht des verteilten logischen Vorgangs mit `operation_Id`, `operation_parentId` und `request.id` mit `dependency.id` erstellen. Diese Felder definieren auch die Kausalitätsreihenfolge der Telemetrieaufrufe.

In Microserviceumgebungen können Ablaufverfolgungen von Komponenten an unterschiedliche Speicherelemente weitergeleitet werden. Jede Komponente kann einen eigenen Instrumentierungsschlüssel in Application Insights aufweisen. Um Telemetriedaten für den logischen Vorgang abzurufen, ruft Application Insights Daten von jedem Speicherelement ab. Wenn die Anzahl der Speicherelemente groß ist, benötigen Sie einen Hinweis, wo Sie als Nächstes suchen sollen. Zur Behebung dieses Problems definiert das Application Insights-Datenmodell zwei Felder: `request.source` und `dependency.target`. Das erste Feld identifiziert die Komponente, die die Abhängigkeitsanforderung initiiert hat. Das zweite Feld identifiziert die Komponente, die die Antwort des Abhängigkeitsaufrufs zurückgegeben hat.

## <a name="example"></a>Beispiel

Schauen wir uns ein Beispiel an. Eine Anwendung namens Stock Prices gibt den aktuellen Marktpreis einer Aktie mithilfe einer externen API mit dem Namen Stock an. Die Anwendung Stock Prices enthält die Seite „Stock“, die vom Clientwebbrowser mit `GET /Home/Stock` geöffnet wird. Die Anwendung fragt die Stock-API mit dem HTTP-Aufruf `GET /api/stock/value` ab.

Sie können die daraus resultierenden Telemetriedaten durch Ausführen einer Abfrage analysieren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Beachten Sie, dass in den Ergebnissen alle Telemetrieelemente die Stamm-`operation_Id` nutzen. Wenn ein Ajax-Aufruf über die Seite erfolgt, wird der Abhängigkeitstelemetrie eine neue eindeutige ID (`qJSXU`) zugewiesen, und die ID der pageView dient als `operation_ParentId`. Die Serveranforderung verwendet dann die Ajax-ID als `operation_ParentId`.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Wenn der Aufruf `GET /api/stock/value` an einen externen Dienst erfolgt, müssen Sie die Identität dieses Servers kennen, damit Sie das Feld `dependency.target` entsprechend festlegen können. Wenn der externe Dienst keine Überwachung unterstützt, wird `target` auf den Hostnamen des Diensts festgelegt (z.B. `stock-prices-api.com`). Wenn sich dieser Dienst jedoch durch Zurückgeben eines vordefinierten HTTP-Headers identifiziert, enthält `target` die Dienstidentität, mit der Application Insights verteilte Ablaufverfolgungen durch Abfragen von Telemetriedaten von diesem Dienst erstellen kann.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Korrelationsheader mit W3C TraceContext

Application Insights geht zum [W3C Trace-Context](https://w3c.github.io/trace-context/) über, der Folgendes definiert:

- `traceparent`: Trägt die global eindeutige Vorgangs-ID und den eindeutigen Bezeichner des Aufrufs.
- `tracestate`: Trägt einen systemspezifischen Ablaufverfolgungskontext.

Die neueste Version des Application Insights SDK unterstützt das Trace-Context-Protokoll, aber möglicherweise müssen Sie sich explizit dafür entscheiden. (Abwärtskompatibilität mit dem vorherigen Korrelationsprotokoll, das vom Application Insights SDK unterstützt wird, bleibt erhalten.)

Das [Korrelations-HTTP-Protokoll, das auch als „Request-ID“ bezeichnet wird](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), ist veraltet. Dieses Protokoll definiert zwei Header:

- `Request-Id`: Enthält die global eindeutige ID des Aufrufs.
- `Correlation-Context`: Enthält die Sammlung von Name/Wert-Paaren der Eigenschaften von verteilten Ablaufverfolgungen.

Application Insights definiert ferner die [Erweiterung](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) für das Korrelations-HTTP-Protokoll. Er verwendet Name/Wert-Paare für `Request-Context`, die die vom unmittelbaren Aufrufer oder Aufgerufenen verwendete Sammlung von Eigenschaften propagieren. Das Application Insights SDK legt mithilfe dieses Headers die Felder `dependency.target` und `request.source` fest.

Der [W3C Trace-Context](https://w3c.github.io/trace-context/) und Application Insights-Datenmodelle sind in folgender Weise zugeordnet:

| Application Insights                   | W3C TraceContext                                      |
|------------------------------------    |-------------------------------------------------|
| `Id` der `Request` und `Dependency`     | [parent-id](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [trace-id](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | [parent-id](https://w3c.github.io/trace-context/#parent-id) der übergeordneten Spanne dieser Spanne. Wenn es sich um eine Stammspanne handelt, muss dieses Feld leer sein.     |


Weitere Informationen finden Sie unter [Application Insights-Telemetriedatenmodell](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>Aktivieren der Unterstützung der verteilten W3C-Ablaufverfolgung für .NET-Apps

Die W3C TraceContext-basierte verteilte Ablaufverfolgung ist in allen aktuellen .NET Framework/.NET Core SDKs standardmäßig aktiviert, zusammen mit der Abwärtskompatibilität mit dem „Request-Id“-Legacyprotokoll.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Aktivieren der Unterstützung für die verteilte Ablaufverfolgung von W3C für Java-Apps

#### <a name="java-30-agent"></a>Java 3.0-Agent

  Der Java 3.0-Agent unterstützt standardmäßig W3C, und es ist keine zusätzliche Konfiguration erforderlich. 

#### <a name="java-sdk"></a>Java-SDK
- **Eingangskonfiguration**

  - Fügen Sie für Java EE-Apps dem Tag `<TelemetryModules>` in „ApplicationInsights.xml“ Folgendes hinzu:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Für Spring Boot-Apps fügen Sie die folgenden Eigenschaften hinzu:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Ausgangskonfiguration**

  Fügen Sie der Datei „AI-Agent.xml“ Folgendes hinzu:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Der Abwärtskompatibilitätsmodus ist standardmäßig aktiviert, und der Parameter `enableW3CBackCompat` ist optional. Verwenden sie ihn nur, wenn Sie die Abwärtskompatibilität deaktivieren möchten.
  >
  > Idealerweise deaktivieren Sie ihn, wenn alle Dienste auf neuere Versionen von SDKs aktualisiert wurden, die das W3C-Protokoll unterstützen. Es wird dringend empfohlen, so bald wie möglich zu diesen neueren SDKs zu wechseln.

> [!IMPORTANT]
> Stellen Sie sicher, dass die Eingangs- und Ausgangskonfiguration genau gleich sind.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Aktivieren der Unterstützung der verteilten W3C-Ablaufverfolgung für Web-Apps

Dieses Feature befindet sich in `Microsoft.ApplicationInsights.JavaScript`. Standardmäßig ist es deaktiviert. Um es zu aktivieren, verwenden Sie die Konfiguration von `distributedTracingMode`. AI_AND_W3C wird für Abwärtskompatibilität mit beliebigen mit älteren Diensten bereitgestellt, die von Application Insights instrumentiert werden.

- **[npm-basiertes Setup](./javascript.md#npm-based-setup)**

Fügen Sie die folgende Konfiguration hinzu:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```
  
- **[Ausschnittbasiertes Setup](./javascript.md#snippet-based-setup)**

Fügen Sie die folgende Konfiguration hinzu:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> Alle Konfigurationen, die zum Aktivieren der Korrelation erforderlich sind, finden Sie in der [Dokumentation zur JavaScript-Korrelation](./javascript.md#enable-correlation).

## <a name="telemetry-correlation-in-opencensus-python"></a>Telemetriekorrelation in OpenCensus Python

OpenCensus Python unterstützt [W3C Trace-Context](https://w3c.github.io/trace-context/), ohne dass eine zusätzliche Konfiguration erforderlich ist.

Das OpenCensus-Datenmodell zu Referenzzwecken finden Sie [hier](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace).

### <a name="incoming-request-correlation"></a>Korrelation eingehender Anforderungen

OpenCensus Python korreliert W3C Trace-Context-Header von eingehenden Anforderungen mit den Spannen, die aus den Anforderungen selbst generiert werden. OpenCensus führt dies automatisch mit Integrationen für die folgenden gängigen Webanwendungsframeworks aus: Flask, Django und Pyramid. Sie müssen lediglich die W3C Trace-Context-Header mit dem [korrekten Format](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) auffüllen und mit der Anforderung senden. Hier eine Flask-Beispielanwendung zur Veranschaulichung:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Mit diesem Code wird eine Flask-Beispielanwendung auf dem lokalen Computer ausgeführt, der an Port `8080` lauscht. Zum Korrelieren des Ablaufverfolgungskontexts senden Sie eine Anforderung an den Endpunkt. In diesem Beispiel können Sie einen `curl`-Befehl verwenden:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Durch einen Blick auf das [Format des Kontextheaders für die Ablaufverfolgung](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) können Sie die folgenden Informationen ableiten:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Wenn Sie sich den Anforderungseintrag ansehen, der an Azure Monitor gesendet wurde, sind dort Felder enthalten, die mit den Informationen des Ablaufverfolgungsheaders aufgefüllt wurden. Diese Daten sind in der Azure Monitor Application Insights-Ressource unter „Protokolle (Analytics)“ zu finden.

![Anfordern von Telemetriedaten in „Protokolle (Analytics)“](./media/opencensus-python/0011-correlation.png)

Das Feld `id` hat das Format `<trace-id>.<span-id>`. Hierbei wird `trace-id` aus dem in der Anforderung übergebenen Ablaufverfolgungsheader entnommen, und `span-id` ist ein generiertes 8-Byte-Array für diese Spanne.

Das Feld `operation_ParentId` hat das Format `<trace-id>.<parent-id>`. Hierbei werden sowohl `trace-id` als auch `parent-id` aus dem in der Anforderung übergebenen Ablaufverfolgungsheader entnommen.

### <a name="log-correlation"></a>Protokollkorrelation

Mit OpenCensus Python können Sie Protokolle korrelieren, indem Sie Protokolldatensätzen eine Ablaufverfolgungs-ID, eine Spannen-ID und ein Stichprobenflag hinzufügen. Sie fügen diese Attribute hinzu, indem Sie die OpenCensus-[Protokollintegration](https://pypi.org/project/opencensus-ext-logging/) installieren. `LogRecord`-Objekten von Python werden die folgenden Attribute hinzugefügt: `traceId`, `spanId` und `traceSampled`. Beachten Sie, dass dies nur für Protokollierungen wirksam wird, die nach der Integration erstellt werden.

Hier eine Beispielanwendung zur Veranschaulichung:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Bei Ausführung dieses Codes wird Folgendes in der Konsole ausgegeben:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Beachten Sie, dass für die Protokollnachricht, die innerhalb der Spanne liegt, eine `spanId` vorhanden ist. Diese entspricht der `spanId`, die zur Spanne mit dem Namen `hello` gehört.

Sie können die Protokolldaten mithilfe von `AzureLogHandler`exportieren. [hier finden Sie weitere Informationen](./opencensus-python.md#logs)

## <a name="telemetry-correlation-in-net"></a>Telemetriekorrelation in .NET

.NET-Runtime unterstützt die verteilte Telemetriekorrelation mithilfe von [Activity](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) und [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

Das Application Insights .NET SDK verwendet `DiagnosticSource` und `Activity` , um Telemetriedaten zu sammeln und zu korrelieren.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Telemetriekorrelation in Java

Die automatische Korrelation von Telemetriedaten wird vom [Java-Agent](./java-in-process-agent.md) und vom [Java SDK](../../azure-monitor/app/java-get-started.md) (Version 2.0.0 oder höher) unterstützt. Das SDK füllt `operation_id` automatisch für alle Telemetriedaten (z.B. Ablaufverfolgungen, Ausnahmen und benutzerdefinierte Ereignisse) auf, die im Zusammenhang mit einer Anforderung ausgegeben werden. Es gibt außerdem die Korrelationsheader (weiter oben beschrieben) für Dienst-zu-Dienst-Aufrufe über HTTP weiter, wenn der [Java SDK-Agent](../../azure-monitor/app/java-agent.md) konfiguriert ist.

> [!NOTE]
> Der Java-Agent von Application Insights erfasst automatisch Anforderungen und Abhängigkeiten für JMS, Kafka, Netty/Webflux usw. Beim Java SDK werden für die Korrelationsfunktion nur Aufrufe unterstützt, die per Apache HttpClient erfolgen. Die automatische Kontextweitergabe über verschiedene Messagingtechnologien (z. B. Kafka, RabbitMQ und Azure Service Bus) wird im SDK nicht unterstützt. 

> [!NOTE]
> Um benutzerdefinierte Telemetriedaten zu erfassen, müssen Sie die Anwendung mit dem Java 2.6 SDK instrumentieren. 

### <a name="role-names"></a>Rollennamen

Sie möchten möglicherweise die Art und Weise anpassen, wie Komponentennamen in der [Anwendungsübersicht](../../azure-monitor/app/app-map.md) angezeigt werden. Dazu können Sie `cloud_RoleName` manuell festlegen, indem Sie eine der folgenden Aktionen ausführen:

- Legen Sie für den Application Insights Java-Agent 3.0 den Cloudrollennamen wie folgt fest:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    Sie können den Cloudrollennamen auch mithilfe der Umgebungsvariablen `APPLICATIONINSIGHTS_ROLE_NAME` festlegen.

- Mit dem Application Insights Java SDK 2.5.0 und höher können Sie `cloud_RoleName` angeben, indem Sie der Datei „ApplicationInsights.xml“ den Eintrag `<RoleName>` hinzufügen:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Bei Verwendung von Spring Boot mit Application Insights Spring Boot Starter müssen Sie nur Ihren benutzerdefinierten Namen für die Anwendung in der Datei „application.properties“ festlegen:

  `spring.application.name=<name-of-app>`

  Spring Boot Starter weist `cloudRoleName` automatisch den Wert zu, den Sie für die `spring.application.name`-Eigenschaft eingeben.

## <a name="next-steps"></a>Nächste Schritte

- Schreiben Sie [benutzerdefinierte Telemetriedaten](../../azure-monitor/app/api-custom-events-metrics.md).
- Informationen zu Szenarien mit erweiterter Korrelation in ASP.NET Core und ASP.NET finden Sie unter [Nachverfolgen von benutzerdefinierten Vorgängen](custom-operations-tracking.md).
- Erfahren Sie mehr über das [Festlegen von cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) für andere SDKs.
- Integrieren Sie alle Komponenten Ihres Microservices in Application Insights. Sehen Sie sich die [unterstützten Plattformen](./platforms.md) an.
- Lesen Sie die Informationen zu den Application Insights-Typen im [Datenmodell](./data-model.md).
- Informationen zum [Erweitern und Filtern von Telemetriedaten](./api-filtering-sampling.md).
- Lesen Sie die [Konfigurationsreferenz für Application Insights](configuration-with-applicationinsights-config.md).