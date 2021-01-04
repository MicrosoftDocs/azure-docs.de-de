---
title: Überwachen von Node.js-Diensten per Azure Application Insights | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Leistung überwachen und Probleme in Node.js-Diensten mit Application Insights diagnostizieren.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7aea6c03b0ce35fa0e74c39ff5f94f714447ad6f
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920585"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Überwachen Ihrer Node.js-Dienste und -Apps mit Application Insights

Mit [Application Insights](./app-insights-overview.md) werden Ihre Back-End-Dienste und -Komponenten nach der Bereitstellung überwacht, damit Sie Leistungsprobleme und andere Probleme erkennen und schnell diagnostizieren können. Sie können Application Insights für Node.js-Dienste verwenden, die in Ihrem Rechenzentrum, auf virtuellen Azure-Computern, in Web-Apps und sogar in anderen öffentlichen Clouds gehostet werden.

Fügen Sie das SDK in Ihren Code ein, und richten Sie dann in Azure eine entsprechende Application Insights-Ressource ein, um Ihre Überwachungsdaten zu empfangen, zu speichern und zu untersuchen. Das SDK sendet Daten zur weiteren Analyse und Untersuchung an diese Ressource.

Das Node.js SDK kann ein- und ausgehende HTTP-Anforderungen, Ausnahmen und einige Systemmetriken automatisch überwachen. Ab Version 0.20 kann das SDK auch einige [Drittanbieterpakete](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules) überwachen, z. B. MongoDB, MySQL und Redis. Alle Ereignisse, die sich auf eine eingehende HTTP-Anforderung beziehen, werden zur Beschleunigung der Problembehandlung korreliert.

Sie können die TelemetryClient-API verwenden, um weitere Aspekte Ihrer App und Ihres Systems manuell zu instrumentieren und zu überwachen. Die TelemetryClient-API wird weiter unten in diesem Artikel näher beschrieben.

## <a name="get-started"></a>Erste Schritte

Führen Sie die folgenden Aufgaben durch, um die Überwachung für eine App oder einen Dienst einzurichten.

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zuerst sicher, dass Sie über ein Azure-Abonnement verfügen, oder [beschaffen Sie sich kostenlos ein neues Abonnement][azure-free-offer]. Falls Ihre Organisation bereits über ein Azure-Abonnement verfügt, kann Ihr Administrator Sie anhand [dieser Anleitung][add-aad-user] hinzufügen.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a>Einrichten einer Application Insights-Ressource

1. Melden Sie sich beim [Azure-Portal][portal] an.
2. [Erstellen Sie eine Application Insights-Ressource](create-new-resource.md).

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a>Einrichten des Node.js-SDK

Fügen Sie das SDK in Ihre App ein, damit Daten gesammelt werden können.

> [!IMPORTANT]
> Neue Azure-Regionen **erfordern** die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. Die [Verbindungszeichenfolge](./sdk-connection-string.md?tabs=nodejs) identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.

1. Kopieren Sie den Instrumentierungsschlüssel der Ressource (auch *iKey* genannt) aus der neu erstellten Ressource. Der ikey wird von Application Insights verwendet, um Ihrer Azure-Ressource Daten zuzuordnen. Bevor Ihr ikey vom SDK verwendet werden kann, müssen Sie ihn in einer Umgebungsvariablen oder im Code angeben.  

   ![Kopieren des Instrumentierungsschlüssels](./media/nodejs/instrumentation-key-001.png)

2. Fügen Sie die Node.js-SDK-Bibliothek per „package.json“ den Abhängigkeiten Ihrer App hinzu. Führen Sie im Stammordner Ihrer App Folgendes aus:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > Installieren Sie bei Verwendung von TypeScript keine separaten Pakete mit Typisierungen. Dieses npm-Paket enthält integrierte Typisierungen.

3. Laden Sie die Bibliothek explizit im Code. Da das SDK die Instrumentierung auch in viele andere Bibliotheken einbettet, sollten Sie die Bibliothek so früh wie möglich laden – noch vor anderen `require`-Anweisungen.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  Sie können einen Instrumentierungsschlüssel auch über die Umgebungsvariable `APPINSIGHTS_INSTRUMENTATIONKEY` bereitstellen, anstatt ihn manuell an `setup()` oder `new appInsights.TelemetryClient()` zu übergeben. Mit dieser Vorgehensweise können Sie ikeys aus festgelegtem Quellcode heraushalten und unterschiedliche ikeys für unterschiedliche Umgebungen angeben. Rufen Sie für die manuelle Konfiguration `appInsights.setup('[your ikey]');` auf.

    Weitere Konfigurationsoptionen finden Sie in den folgenden Abschnitten.

    Sie können das SDK auch ohne das Senden von Telemetriedaten testen, indem Sie `appInsights.defaultClient.config.disableAppInsights = true` festlegen.

5. Rufen Sie `appInsights.start();` auf, um mit dem automatischen Sammeln und Senden von Daten zu beginnen.

### <a name="monitor-your-app"></a><a name="monitor"></a> Überwachen Ihrer App

Das SDK sammelt automatisch Telemetriedaten zur Node.js-Laufzeit und zu einigen gängigen Drittanbietermodulen. Verwenden Sie Ihre Anwendung, um einige dieser Daten zu generieren.

Navigieren Sie im [Azure-Portal][portal] dann zu der zuvor erstellten Application Insights-Ressource. Suchen Sie in der **Übersichtszeitachse** nach Ihren ersten Datenpunkten. Wählen Sie in den Diagrammen verschiedene Komponenten aus, um detailliertere Daten anzuzeigen.

Sie können die Topologie, die für Ihre App ermittelt wird, über die [Anwendungsübersicht](app-map.md) anzeigen.

#### <a name="no-data"></a>Keine Daten

Da das SDK Daten zur Übermittlung in Batches zusammenfasst, kann es ggf. eine Weile dauern, bis die Elemente im Portal angezeigt werden. Falls in Ihrer Ressource keine Daten angezeigt werden, können Sie folgende Lösungsmöglichkeiten ausprobieren:

* Fahren Sie mit der Verwendung der Anwendung fort. Führen Sie mehr Aktionen durch, um mehr Telemetriedaten zu generieren.
* Klicken Sie in der Ressourcenansicht des Portals auf **Aktualisieren**. Diagramme aktualisieren sich regelmäßig selbst, aber bei der manuellen Aktualisierung wird der Vorgang sofort erzwungen.
* Stellen Sie sicher, dass die [erforderlichen ausgehenden Ports](./ip-addresses.md) geöffnet sind.
* Verwenden Sie [Suchen](./diagnostic-search.md), um nach bestimmten Ereignissen zu suchen.
* Lesen Sie die [häufig gestellten Fragen][FAQ].

## <a name="basic-usage"></a>Grundlegende Verwendung

Für die sofort einsetzbare Sammlung von HTTP-Anforderungen, Ereignissen von beliebten Drittanbieterbibliotheken, nicht behandelten Ausnahmen und Systemmetriken:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Ist der Instrumentierungsschlüssel in der Umgebungsvariable `APPINSIGHTS_INSTRUMENTATIONKEY` festgelegt, kann `.setup()` ohne Argumente aufgerufen werden. Dies erleichtert die Verwendung verschiedener Instrumentierungsschlüssel für unterschiedliche Umgebungen.

Laden Sie noch vor anderen Paketen die Application Insights-Bibliothek (`require("applicationinsights")`) so früh wie möglich in Ihre Skripts. Dies ist erforderlich, damit die Application Insights-Bibliothek spätere Pakete für die Nachverfolgung vorbereiten kann. Treten Konflikte mit anderen Bibliotheken auf, die eine ähnliche Vorbereitung durchführen, laden Sie die Application Insights-Bibliothek nach diesen anderen Bibliotheken.

Aufgrund der Art und Weise, wie JavaScript Rückrufe verarbeitet, sind für die Verfolgung einer Anforderung über mehrere externe Abhängigkeiten und spätere Rückrufe hinweg zusätzliche Arbeitsschritte erforderlich. Diese zusätzliche Nachverfolgung ist standardmäßig aktiviert. Rufen Sie wie im Abschnitt [Konfiguration](#sdk-configuration) unten beschrieben `setAutoDependencyCorrelation(false)` auf, um die Funktion zu deaktivieren.

## <a name="migrating-from-versions-prior-to-022"></a>Migrieren von Versionen vor Version 0.22

Mit Version 0.22 wurden wichtige Änderungen eingeführt. Mit diesen Änderungen wurde die Konsistenz mit anderen Application Insights SDKs erhöht, und künftige Erweiterungen wurden ermöglicht.

Im Allgemeinen können Sie die Migration auf folgende Weise durchführen:

- Ersetzen Sie Verweise auf `appInsights.client` durch `appInsights.defaultClient`.
- Ersetzen Sie Verweise auf `appInsights.getClient()` durch `new appInsights.TelemetryClient()`.
- Ersetzen Sie alle Argumente für „client.track*“-Methoden durch ein einzelnes Objekt, das als Argumente benannte Eigenschaften enthält. Das erwartete Objekt der einzelnen Telemetrietypen finden Sie in den integrierten Typhinweisen Ihrer IDE oder unter [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes).

Wenn Sie ohne Verkettung mit `appInsights.setup()` auf die SDK-Konfigurationsfunktionen zugreifen, befinden sich diese Funktionen jetzt unter `appInsights.Configurations` (z. B. `appInsights.Configuration.setAutoCollectDependencies(true)`). Informationen zu Änderungen an der Standardkonfiguration finden Sie im nächsten Abschnitt.

## <a name="sdk-configuration"></a>SDK-Konfiguration

Das `appInsights`-Objekt stellt eine Reihe von Konfigurationsmethoden bereit. Diese werden im folgenden Codeausschnitt mit den jeweiligen Standardwerten aufgelistet.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Legen Sie `.setAutoDependencyCorrelation(true)` fest, um die Ereignisse für einen Dienst vollständig zu korrelieren. Wenn diese Option festgelegt ist, kann das SDK den Kontext übergreifend für asynchrone Rückrufe in Node.js nachverfolgen.

Informationen zu den Methoden und optionalen sekundären Argumenten finden Sie in den Beschreibungen der integrierten Typhinweise Ihrer IDE oder unter [applicationinsights.ts](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts).

> [!NOTE]
>  Mit der Standardkonfiguration von `setAutoCollectConsole` werden Aufrufe von `console.log` (sowie anderer Konsolenmethoden) *ausgeschlossen*. Es werden nur Aufrufe von unterstützten Protokollierungen von Drittanbietern (wie Winston und Bunyan) gesammelt. Mit `setAutoCollectConsole(true, true)` können Sie dieses Verhalten ändern, um künftig Aufrufe von `console`-Methoden einzuschließen.

### <a name="sampling"></a>Stichproben

Standardmäßig sendet das SDK alle gesammelten Daten an den Application Insights-Dienst. Wenn Sie viele Daten sammeln, können Sie die Erstellung von Stichproben aktivieren, um die Menge der gesendete Daten zu verringern. Legen Sie hierzu das Feld `samplingPercentage` des `config`-Objekts eines Clients fest. Wenn Sie dabei `samplingPercentage` auf „100“ festlegen (Standardeinstellung), werden alle Daten gesendet. Bei „0“ werden keine Daten gesendet.

Bei der automatischen Korrelation werden alle Daten, die einer einzelnen Anforderung zugeordnet sind, als Einheit ein- oder ausgeschlossen.

Fügen Sie etwa folgenden Code hinzu, um die Stichprobenerstellung zu aktivieren:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Mehrere Rollen für Anwendungen mit mehreren Komponenten

Angenommen, Ihre Anwendung besteht aus mehreren Komponenten, die alle mit demselben Instrumentierungsschlüssel instrumentiert werden sollen. Doch diese Komponenten werden im Portal noch immer als separate Einheiten angezeigt – ganz so, als ob separate Instrumentierungsschlüssel genutzt werden sollten (etwa als separate Knoten in der Anwendungsübersicht). Konfigurieren Sie in diesem Fall das Feld „RoleName“ manuell, um die Telemetriedaten der einzelnen Komponenten von denen anderer Komponenten, die Daten an Ihre Application Insights-Ressource senden, unterscheiden zu können.

Legen Sie das Feld „RoleName“ auf folgende Weise fest:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Automatische Instrumentierung von Drittanbietern

Damit Kontext über mehrere asynchrone Aufrufe hinweg nachverfolgt werden kann, müssen Sie in Drittanbieterbibliotheken wie MongoDB und Redis einige Änderungen vornehmen. Standardmäßig verwendet Application Insights [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers), um einige dieser Bibliotheken mit einem Monkey-Patch zu versehen. Sie können diese Einstellung ändern, indem Sie die Umgebungsvariable `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` festlegen.

> [!NOTE]
> Wenn Sie diese Umgebungsvariable festlegen, werden Ereignisse möglicherweise dem entsprechenden Vorgang nicht mehr korrekt zugeordnet.

 Sie können einzelne Monkey-Patches deaktivieren, indem Sie die Umgebungsvariable `APPLICATION_INSIGHTS_NO_PATCH_MODULES` auf eine durch Kommas getrennte Liste mit Paketen festlegen, die deaktiviert werden sollen (z. B. `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis`). Auf diese Weise werden die Pakete `console` und `redis` nicht mit einem Patch versehen.

Derzeit werden die folgenden neun Pakete instrumentiert: `bunyan`, `console`, `mongodb`, `mongodb-core`, `mysql`, `redis`, `winston`, `pg` und `pg-pool`. Eine Auflistung der Versionen, die mit einem Patch versehen werden, finden Sie in der [Infodatei zu diagnostic-channel-publishers](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md).

Je nachdem, ob `setAutoCollectConsole` aktiviert ist oder nicht, werden durch die `bunyan`-, `winston`- und `console`-Patches Application Insights-Ablaufverfolgungsereignisse generiert. Die restlichen Patches generieren bei Aktivierung von `setAutoCollectDependencies` Application Insights-Abhängigkeitsereignisse.

### <a name="live-metrics"></a>Livemetriken

Verwenden Sie `setSendLiveMetrics(true)`, um Livemetriken von Ihrer App an Azure zu senden. Das Filtern von Livemetriken wird im Portal derzeit nicht unterstützt.

### <a name="extended-metrics"></a>Erweiterte Metriken

> [!NOTE]
> Ab Version 1.4.0 wird das Senden von erweiterten nativen Metriken unterstützt.

Wenn Sie das Senden von erweiterten nativen Metriken von Ihrer App an Azure aktivieren möchten, installieren Sie das separate Paket für native Metriken. Das SDK wird nach der Installation automatisch geladen und beginnt mit dem Sammeln von nativen Node.js-Metriken.

```bash
npm install applicationinsights-native-metrics
```

Derzeit sammelt das Paket für native Metriken automatisch die CPU-Zeit für die automatische Speicherbereinigung, für die Takte von Ereignisschleifen sowie für die Heapnutzung:

- **Automatische Speicherbereinigung**: Die für die einzelnen Typen der automatischen Speicherbereinigung aufgewendete CPU-Zeit sowie die Anzahl der Vorkommen der einzelnen Typen.
- **Ereignisschleife**: Die Anzahl der Takte sowie die insgesamt aufgewendete CPU-Zeit.
- **Heapnutzung und Nicht-Heapnutzung**: Anteil der Heap-Speicherauslastung Ihrer App.

### <a name="distributed-tracing-modes"></a>Modi der verteilten Ablaufverfolgung

Standardmäßig sendet das SDK Header, die von anderen Anwendungen/Diensten, die mit einem Application Insights SDK instrumentiert sind, gelesen werden. Optional können Sie das Senden/Empfangen von [W3C-Headern für Ablaufverfolgungskontext](https://github.com/w3c/trace-context) neben den vorhandenen KI-Headern aktivieren, damit Korrelationen mit vorhandenen Legacydiensten nicht unterbrochen werden. Durch Aktiveren von W3C-Headern kann Ihre App eine Korrelation mit anderen Diensten herstellen, die zwar nicht mit Application Insights instrumentiert sind, aber diesen W3C-Standard übernehmen.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient-API

Eine vollständige Beschreibung der TelemetryClient-API finden Sie unter [Application Insights-API für benutzerdefinierte Ereignisse und Metriken](./api-custom-events-metrics.md).

Sie können alle Anforderungen, Ereignisse, Metriken oder Ausnahmen mit dem Application Insights-Node.js-SDK nachverfolgen. Im folgenden Codebeispiel werden einige APIs veranschaulicht, die Sie verwenden können:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Nachverfolgen Ihrer Abhängigkeiten

Nutzen Sie den folgenden Code, um Ihre Abhängigkeiten nachzuverfolgen:

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Das folgende Beispiel zeigt ein Hilfsprogramm, das mithilfe von `trackMetric` die Dauer der Zeitplanung von Ereignisschleifen misst:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Hinzufügen einer benutzerdefinierten Eigenschaft zu allen Ereignissen

Verwenden Sie den folgenden Code, um allen Ereignissen eine benutzerdefinierte Eigenschaft hinzuzufügen:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Nachverfolgen von HTTP GET-Anforderungen

Verwenden Sie den folgenden Code, um HTTP GET-Anforderungen manuell nachzuverfolgen:

> [!NOTE]
> Standardmäßig werden alle Anforderungen nachverfolgt. Rufen Sie vor dem Aufruf von „start()“ den Befehl „.setAutoCollectRequests(false)“ auf, um die automatische Sammlung zu deaktivieren.

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

Alternativ dazu können Sie Anforderungen auch mithilfe der `trackNodeHttpRequest`-Methode nachverfolgen:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>Nachverfolgen der Serverstartzeit

Verwenden Sie den folgenden Code, um die Serverstartzeit nachzuverfolgen:

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="preprocess-data-with-telemetry-processors"></a>Vorverarbeiten von Daten mit Telemetrieprozessoren

Mithilfe von *Telemetrieprozessoren* können Sie die gesammelten Daten vor der Aufbewahrung verarbeiten und filtern. Telemetrieprozessoren werden nacheinander in der Reihenfolge aufgerufen, in der sie hinzugefügt wurden, bevor das Telemetrieelement an die Cloud gesendet wird.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Gibt ein Telemetrieprozessor „FALSE“ zurück, wird das entsprechende Telemetrieelement nicht gesendet.

Alle Telemetrieprozessoren erhalten zur Überprüfung und Änderung die Telemetriedaten und deren Umschlag. Zudem erhalten sie auch ein Kontextobjekt. Der Inhalt dieses Objekts wird durch den `contextObjects`-Parameter beim Aufruf einer Nachverfolgungsmethode für manuell nachverfolgte Telemetriedaten definiert. Für automatisch gesammelte Telemetriedaten wird dieses Objekt mit den verfügbaren Anforderungsinformationen und dem permanenten Anforderungsinhalt aufgefüllt, die von `appInsights.getCorrelationContext()` bereitgestellt werden (wenn die automatische Abhängigkeitskorrelation aktiviert ist).

Der TypeScript-Typ eines Telemetrieprozessors lautet:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Ein Prozessor zum Entfernen von Daten der Stapelüberwachung aus Ausnahmen könnte beispielsweise folgendermaßen aussehen:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Verwenden mehrerer Instrumentierungsschlüssel

Sie können mehrere Application Insights-Ressourcen erstellen, denen jeweils unterschiedliche Daten gesendet werden, wenn Sie für die einzelnen Ressourcen den entsprechenden Instrumentierungsschlüssel („iKey“) verwenden.

 Beispiel:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Erweiterte Konfigurationsoptionen

Das Clientobjekt enthält eine `config`-Eigenschaft mit vielen optionalen Einstellungen für erweiterte Szenarios. Diese Eigenschaften können auf folgende Weise festgelegt werden:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Diese Eigenschaften sind clientspezifisch, d. h. dass Sie `appInsights.defaultClient`-Clients und `new appInsights.TelemetryClient()`-Clients getrennt voneinander konfigurieren können.

| Eigenschaft                        | BESCHREIBUNG                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Ein Bezeichner für Ihre Application Insights-Ressource.                                                      |
| endpointUrl                     | Der Erfassungsendpunkt, an den die Telemetrienutzlasten gesendet werden sollen.                                                      |
| quickPulseHost                  | Der Host für Live Metrics Stream, an den die Telemetriedaten der Livemetriken gesendet werden sollen.                                            |
| proxyHttpUrl                    | Ein Proxyserver für den HTTP-Datenverkehr des SDK (optional; der Standardwert wird aus der Umgebungsvariable `http_proxy` abgerufen).     |
| proxyHttpsUrl                   | Ein Proxyserver für den HTTPS-Datenverkehr des SDK (optional; der Standardwert wird aus der Umgebungsvariable `https_proxy` abgerufen).   |
| httpAgent                       | Ein HTTP-Agent für den HTTP-Datenverkehr des SDK (optional; ist standardmäßig nicht definiert).                                   |
| httpsAgent                      | Ein HTTP-Agent für den HTTPS-Datenverkehr des SDK (optional; ist standardmäßig nicht definiert).                                 |
| maxBatchSize                    | Die maximale Anzahl von Telemetrieelementen, die in eine Nutzlast zum Erfassungsendpunkt eingeschlossen werden sollen (Standardwert: `250`).   |
| maxBatchIntervalMs              | Die maximale Wartezeit, bis eine Nutzlast „maxBatchSize“ erreicht (Standardwert: `15000`).               |
| disableAppInsights              | Ein Flag, das angibt, ob die Übertragung von Telemetriedaten deaktiviert ist (Standardwert: `false`).                                 |
| samplingPercentage              | Der Prozentsatz der nachverfolgten Telemetrieelemente, der übertragen werden soll (Standardwert: `100`).                      |
| correlationIdRetryIntervalMs    | Die Wartezeit bis zum erneuten Versuch, die ID für komponentenübergreifende Korrelationen abzurufen (Standardwert: `30000`).     |
| correlationHeaderExcludedDomains| Eine Liste der Domänen, die von Header Injection für komponentenübergreifende Korrelationen ausgeschlossen werden sollen (Standardwert siehe [Config.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen Ihrer Telemetriedaten im Portal](./overview-dashboard.md)
* [Schreiben von Analytics-Abfragen über Ihre Telemetriedaten](../log-query/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md